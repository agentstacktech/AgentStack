# RAG Platform Service — Developer Guide

**TurboQuant · Zero new tables · RAG platform (release track v0.4.3)**

> **Using RAG in the browser?** Start with **[USER_FEATURES_GUIDE.md](USER_FEATURES_GUIDE.md)** (collections, search, memory — user-oriented). This document covers architecture, REST, MCP, and limits for builders and automations.

---

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Layer-by-Layer Description](#layer-by-layer-description)
4. [Data Model — 8DNA](#data-model--8dna-zero-new-tables)
5. [Startup Sequence](#startup-sequence)
6. [REST API Reference](#rest-api-reference)
7. [MCP Tool Catalogue](#mcp-tool-catalogue)
8. [Subscription Tier Matrix](#subscription-tier-matrix)
9. [Developer Flows](#developer-flows)
10. [AI Builder Integration](#ai-builder-integration)
11. [Known Limitations](#known-limitations)

---

## Overview

The RAG Platform Service gives every AI in the AgentStack ecosystem a shared,
semantically searchable memory. It is accessible three ways:

| Client | Entry Point | Auth |
|--------|------------|------|
| HTTP clients, frontend | `POST /api/rag/...` | `require_authentication` |
| **Web dashboard** | `/dashboard/:projectId?module=rag` — collections, ingest, search, session memory (`RAGDashboardWidget`) | same session / project as app |
| Any AI agent | `agentstack.execute` MCP (`rag.*` actions) | project-scoped session |
| AI Builder internally | `RAGEngine.get_context_for_prompt()` | project_id from stage |

**Key properties:**

- **Zero new tables** — uses existing `data_projects_project` and
  `data_projects_backup` with `entity_type` tags.
- **TurboQuant compression** — ~8× memory reduction on embeddings with
  near-zero accuracy loss (FWHT + outlier-aware scalar quantization + QJL residual).
- **Hybrid search** — BM25 sparse + dense TQ inner product, fused with
  Reciprocal Rank Fusion (RRF) and optionally re-ranked by MMR.
- **Subscription-gated** — Free / Starter / Pro / Enterprise limits applied at
  the engine level, not just the API.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                              CLIENTS                                 │
│  REST /api/rag/*    MCP agentstack.execute    AI Builder Stage       │
└──────────┬──────────────────┬────────────────────────┬──────────────┘
           │                  │                        │
           ▼                  ▼                        ▼
┌──────────────────────────────────────────────────────────────────────┐
│                           API LAYER                                  │
│  rag_endpoints.py       tools_rag.py          RAGContextSelector     │
│  (Auth + Ownership)    (11 @mcp_tool)         (Context Optimizer v2) │
└──────────────────────────────────┬───────────────────────────────────┘
                                   │
                                   ▼
┌──────────────────────────────────────────────────────────────────────┐
│                        RAGEngine (singleton)                         │
│   ingest()   search()   memory_add/get/search()   get_context_for_prompt()│
└──┬──────────────────────────────────────────────────────────────────┬┘
   │  Core Layer                                    Persistence Layer  │
   ▼                                                                   ▼
┌──────────────────────────────────┐  ┌────────────────────────────────┐
│  TurboQuantizer  (L0)            │  │  CollectionManager             │
│  TextChunker     (L1)            │  │  → data_projects_project       │
│  EmbedderService (L1)            │  │  DocumentRepository            │
│  TQVectorStore   (L2)            │  │  → data_projects_backup        │
│  MemoryStore     (L4)            │  │  MemoryRepository              │
│                                  │  │  → data_projects_backup        │
└──────────────────────────────────┘  └────────────────────────────────┘
                                                         │
                          NeuralCacheEngine ─────────────┘
                          L1: MemoryTurn deque
                          L2: VectorCacheEntry (TQ-compressed embeddings)
```

---

## Layer-by-Layer Description

### L0 — TurboQuantizer (`shared/rag/turbo_quant.py`)

Implements Google's TurboQuant compression in pure NumPy:

1. **Fast Walsh-Hadamard Transform** — randomised rotation of the vector
   space (O(n log n), pure NumPy, no GPU required).
2. **Outlier-aware scalar quantisation** — detects the top `outlier_ratio`
   dimensions by absolute magnitude; these get 2-bit dedicated channels while
   the rest are quantised to `bits` precision.
3. **QJL 1-bit residual** — a Johnson-Lindenstrauss sketched residual vector
   preserves the unbiased inner-product estimate for ANN search.

API:

```python
tq = TurboQuantizer(bits=4, outlier_ratio=0.05)
codebook = tq.compress(vectors)          # (n, dim) float32 → QuantizationCodebook
approx   = tq.decompress(codebook)       # QuantizationCodebook → (n, dim) float32
scores   = tq.inner_product(query, cb)   # (n,) float32 relevance scores
raw      = TurboQuantizer.serialize(cb)  # bytes
cb2      = TurboQuantizer.deserialize(raw)
```

### L1 — TextChunker (`shared/rag/chunker.py`)

Splits documents into overlapping chunks:

- Default: 512 tokens / 64 token overlap.
- Sentence-boundary aware (never cuts mid-sentence).
- Each chunk carries a `content_hash` (SHA-256[:16]) for progressive indexing.

### L1 — EmbedderService (`shared/rag/embedder.py`)

Provider-agnostic embedding wrapper:

| Provider | Model | Dimensions |
|----------|-------|-----------|
| `openai` | `text-embedding-3-small` | 1536 |
| `gemini` | `text-embedding-004` | 768 |
| `mock` | deterministic hash-based | 128 |

Cache strategy: tries `VectorCacheEntry` (TQ-compressed, ~8× smaller) in
`NeuralCacheEngine.memory_pool`; falls back to `cache.set()` with a plain list.
Cache TTL is 24 h — embeddings are deterministic per model.

### L2 — TQVectorStore (`shared/rag/vector_store.py`)

In-memory vector index per collection:

- **Dense search** — TQ inner product (compressed dot product).
- **Sparse search** — BM25 via `rank_bm25`.
- **Hybrid** — RRF merges dense + sparse ranked lists.
- **MMR** — Maximal Marginal Relevance for result diversity.
- **Serialization** — `to_base64()` / `from_base64()` for optional JSONB storage.

### L3 — CollectionManager / DocumentRepository (`shared/rag/collection_manager.py`)

8DNA CRUD without new tables:

| Class | Table | `entity_type` | Hierarchy |
|-------|-------|--------------|-----------|
| `CollectionManager` | `data_projects_project` | `rag_collection` | project → collection |
| `DocumentRepository` | `data_projects_backup` | `rag_document` | collection → chunk |
| `MemoryRepository` | `data_projects_backup` | `rag_memory` | project → turn |

Progressive indexing: `save_chunk()` computes `content_hash`; unchanged chunks
are skipped — re-ingesting a document only processes modified sections.

### L4 — MemoryStore (`shared/rag/memory_store.py`)

Two-tier conversation memory:

| Tier | Store | Capacity | Retrieval |
|------|-------|---------|-----------|
| Hot | NeuralCacheEngine `rag:mem:{session_id}` | Last 20 turns | O(1) |
| Cold | In-process TQVectorStore per session | Up to 1 000 turns | TQ semantic search |
| Persist | 8DNA `data_projects_backup` | Unlimited | On cold start |

After a cold start, `search()` calls `_hydrate_cold_from_dna()` which
decompresses stored TQ bytes to reconstruct float32 vectors — semantic search
works correctly across server restarts.

### L5 — RAGEngine (`shared/rag/rag_engine.py`)

Central singleton orchestrating all layers.

```python
engine = get_rag_engine()
await engine.initialize(embedding_provider="openai", api_key="sk-...")
```

Public methods:

| Method | Description |
|--------|------------|
| `ingest(collection_id, text, metadata, project_id)` | Chunk → embed → compress → store. Returns `{chunks_added, chunks_skipped}`. |
| `search(collection_id, query, top_k, hybrid, mmr, ...)` | Semantic search with optional contextual compression. |
| `memory_add(session_id, role, content, project_id)` | Add a conversation turn to memory. |
| `memory_get(session_id, limit)` | Get recent turns. |
| `memory_search(session_id, query, top_k)` | Semantic search over past turns. |
| `get_context_for_prompt(task, project_id, token_budget)` | Used by AI Builder — returns ranked snippets within token budget. |

---

## Data Model — 8DNA (Zero New Tables)

### Collection (`data_projects_project`)

```json
{
  "entity_type": "rag_collection",
  "name": "my-kb",
  "description": "Product documentation",
  "config": {
    "embedding_provider": "openai",
    "tq_bits": 4
  },
  "stats": {
    "doc_count": 42,
    "chunk_count": 317,
    "last_indexed_at": "2026-03-27T10:00:00Z"
  }
}
```

`project_id` on the row → multi-tenant isolation.

### Document Chunk (`data_projects_backup`)

```json
{
  "entity_type": "rag_document",
  "parent_uuid": "<collection_uuid>",
  "doc_id": "readme.md__chunk_0",
  "source_doc_id": "readme.md",
  "text": "AgentStack is...",
  "chunk_index": 0,
  "vector_compressed": "<base64 TurboQuant bytes>",
  "content_hash": "a3f2b1c4d5e6f7a8",
  "metadata": { "source": "readme.md", "type": "docs" }
}
```

### Memory Turn (`data_projects_backup`)

```json
{
  "entity_type": "rag_memory",
  "session_id": "sess_abc123",
  "role": "user",
  "content": "How do I add a payment?",
  "vector_compressed": "<base64 TurboQuant bytes>",
  "content_hash": "b2c3d4e5f6a7b8c9"
}
```

---

## Startup Sequence

```
app lifespan (core_app.py)
  │
  ├── get_rag_engine()                    # singleton created
  ├── get_namespaced_cache("rag_embeddings")   # L2 cache namespace
  ├── await engine.initialize(provider, api_key, cache)
  │     ├── EmbedderService(...)
  │     └── MemoryStore(tq, cache, persist=True)
  │
  ├── asyncio.create_task(_index_platform_reference_kb())
  │     └── ingest curated platform markdown → collection `system:philosophy` (legacy id)
  │
  └── (on first GET /mcp/discovery)
        └── asyncio.ensure_future(_index_mcp_tools_rag())
              └── ingest each MCP tool description → collection "system:mcp_tools"
```

---

## REST API Reference

All routes are mounted under `/api/rag/` (no version prefix).
Authentication: `require_authentication` (project-scoped session cookie/token).

### Collections

#### `POST /api/rag/collections`

Create a knowledge base collection.

**Request:**
```json
{
  "name": "product-docs",
  "description": "Product documentation",
  "embedding_provider": "openai"
}
```

**Response:**
```json
{
  "success": true,
  "collection": {
    "uuid": "550e8400-...",
    "project_id": 42,
    "name": "product-docs",
    "config": { "embedding_provider": "openai", "tq_bits": 4 },
    "stats": { "doc_count": 0, "chunk_count": 0, "last_indexed_at": null }
  }
}
```

#### `GET /api/rag/collections`

List all collections for the authenticated project.

#### `DELETE /api/rag/collections/{collection_id}`

Delete collection and all its document chunks (cascade). Ownership verified.

---

### Documents

#### `POST /api/rag/collections/{collection_id}/documents`

Add a document (auto-chunked, embedded, TQ-compressed, persisted).

**Request:**
```json
{
  "content": "Full text of the document...",
  "source_doc_id": "readme.md",
  "metadata": { "type": "docs", "version": "1.0" }
}
```

**Response:**
```json
{
  "success": true,
  "chunks_added": 3,
  "chunks_skipped": 0,
  "collection_id": "550e8400-...",
  "source_doc_id": "readme.md"
}
```

#### `GET /api/rag/collections/{collection_id}/documents?limit=50`

List chunks in a collection.

#### `DELETE /api/rag/collections/{collection_id}/documents/{doc_id}`

Remove all chunks for a source document.

#### `POST /api/rag/collections/{collection_id}/search`

Semantic search.

**Request:**
```json
{
  "query": "How do I configure payments?",
  "top_k": 5,
  "hybrid": true,
  "mmr": false,
  "filters": { "type": "docs" }
}
```

**Response:**
```json
{
  "success": true,
  "query": "How do I configure payments?",
  "results": [
    {
      "doc_id": "readme.md__chunk_2",
      "text": "To configure payments, add the STRIPE_KEY...",
      "score": 0.92,
      "metadata": { "type": "docs" },
      "source_doc_id": "readme.md",
      "chunk_index": 2
    }
  ]
}
```

---

### Memory

#### `POST /api/rag/memory/{session_id}/add`

Store a conversation turn.

**Request:**
```json
{ "role": "user", "content": "How do I add a payment?" }
```

**Response:**
```json
{ "success": true, "session_id": "sess_abc", "turn_index": 4, "role": "user" }
```

#### `GET /api/rag/memory/{session_id}?limit=20`

Get recent turns (hot store first, then DNA).

#### `POST /api/rag/memory/{session_id}/search`

Semantic search over past turns.

**Request:**
```json
{ "query": "payment configuration questions", "top_k": 3 }
```

---

## MCP Tool Catalogue

All tools are invoked via `agentstack.execute` with `action: "rag.<name>"`.

| Action | Description | Key Parameters |
|--------|-------------|---------------|
| `rag.collection_create` | Create a knowledge base | `name`, `project_id`, `embedding_provider` |
| `rag.collection_list` | List collections | `project_id` |
| `rag.collection_delete` | Delete collection + cascade | `collection_id`, `project_id` |
| `rag.document_add` | Ingest a document | `collection_id`, `content`, `source_doc_id`, `metadata` |
| `rag.document_list` | List chunks | `collection_id`, `limit` |
| `rag.document_delete` | Remove a document | `collection_id`, `doc_id` |
| `rag.search` | Semantic search | `collection_id`, `query`, `top_k`, `hybrid`, `mmr` |
| `rag.memory_add` | Add a conversation turn | `session_id`, `role`, `content`, `project_id` |
| `rag.memory_get` | Get recent turns | `session_id`, `limit` |
| `rag.memory_search` | Semantic memory search | `session_id`, `query`, `top_k` |

**Example MCP call:**

```json
{
  "action": "rag.search",
  "collection_id": "550e8400-...",
  "query": "stripe webhook setup",
  "top_k": 3,
  "hybrid": true
}
```

---

## Subscription Tier Matrix

| Feature | Free | Starter | Pro | Enterprise |
|---------|------|---------|-----|-----------|
| Collections | 0 | 1 | 10 | Unlimited |
| Chunks per collection | — | 100 | 10 000 | Unlimited |
| Memory turns per session | 50 | 500 | Unlimited | Unlimited |
| Hybrid search (BM25 + dense) | No | Yes | Yes | Yes |
| MMR re-ranking | No | No | Yes | Yes |
| Contextual compression | No | No | No | Yes |

Limits are enforced in `RAGEngine`:
- `ingest()` checks `collections == 0` (free tier blocks ingestion) and `chunks_per_collection` cap.
- `memory_add()` checks `memory_turns` cap per session.
- `search()` downgrades `hybrid`/`mmr`/`compress_results` flags when not allowed by tier.

The tier is resolved via `_get_tier(project_id)` which calls
`shared.subscription.get_project_tier` if available, otherwise defaults to
`"starter"` (conservative fallback).

---

## Developer Flows

### Flow 1 — Index a document and search

```python
engine = get_rag_engine()
await engine.initialize(embedding_provider="openai", api_key=OPENAI_KEY)

# Ingest
result = await engine.ingest(
    collection_id="my-project:docs",
    text=open("README.md").read(),
    metadata={"source": "README.md"},
    project_id=42,
    source_doc_id="README.md",
)
# {"chunks_added": 4, "chunks_skipped": 0, ...}

# Search
hits = await engine.search(
    collection_id="my-project:docs",
    query="how to configure authentication",
    top_k=5,
    hybrid=True,
    project_id=42,
)
# [{"doc_id": "...", "text": "...", "score": 0.87, ...}, ...]
```

### Flow 2 — Conversational memory

```python
# Add turns
await engine.memory_add("sess_xyz", "user", "What's the weather?", project_id=42)
await engine.memory_add("sess_xyz", "assistant", "It's sunny today.", project_id=42)

# Retrieve recent
turns = await engine.memory_get("sess_xyz", limit=10)

# Semantic search
relevant = await engine.memory_search("sess_xyz", "weather forecast", top_k=3)
```

### Flow 3 — Re-index a changed document (progressive indexing)

Re-calling `ingest()` with the same `source_doc_id` automatically:

1. Removes old in-memory chunks for that `source_doc_id`.
2. Re-embeds only changed chunks (different `content_hash`).
3. Updates the 8DNA backup rows for changed chunks.
4. Skips unchanged chunks entirely.

### Flow 4 — AI Builder context enrichment

The `UnderstandingStage` injects RAG context automatically before every LLM
call (both `handle()` and `handle_streaming()`):

```
user message → RAGEngine.get_context_for_prompt(task=message, project_id=..., token_budget=1500)
             → top-5 snippets from "project:{id}:code_index"
             → prepended as "## Relevant Context (RAG)" to system_prompt
             → LLM call with enriched context
```

For the `RAGContextSelector` (Context Optimizer v2), snippets are injected as
`patterns` into `SDKContext` and scored — `optimize_context()` keeps the
highest-relevance patterns within the token budget.

---

## AI Builder Integration

### Context Optimizer v2 — `RAGContextSelector`

Wraps the existing `ContextSelector` and augments it:

```
base selector ──────────────────┐
                                 ├─ parallel ──► merged SDKContext
RAGEngine.get_context_for_prompt ┘              (RAG patterns scored by relevance)
```

Usage:

```python
from ai_builder.sdk_context.rag_context_selector import RAGContextSelector
from ai_builder.sdk_context import ContextSelector

selector = RAGContextSelector(ContextSelector(...))
context = await selector.select_context(
    task_description="add Stripe payment integration",
    project_id=42,
    max_tokens=4000,
)
```

### System Knowledge Bases

Two system collections are auto-indexed at startup:

| Collection ID | Source | Purpose |
|--------------|--------|---------|
| `system:philosophy` | Curated platform markdown (internal sources) | Semantic search over bundled reference text (legacy collection id) |
| `system:mcp_tools` | All `@mcp_tool` descriptions | Semantic tool discovery |

---

## Known Limitations

1. **Cold start re-embedding** — when `vector_compressed` is missing from a
   stored chunk (legacy data), `_lazy_load_collection` re-embeds the text.
   This costs API calls proportional to the number of affected chunks.

2. **`_get_tier` integration stub** — when `shared.subscription` is not
   importable the engine defaults to `"starter"` limits. Production deployments
   should ensure `shared.subscription.get_project_tier` is wired to the real
   subscription table.

3. **Semantic eviction not active** — `VectorCacheEntry` has the design for
   semantic L2 eviction (remove entries most similar to each other), but the
   `NeuralCacheEngine.memory_pool` still uses plain LRU eviction. This is a
   future enhancement.

4. **`GET /mcp/discovery` double registration** — `routes.py` defines two
   `GET /discovery` handlers on the same router. The first registered wins in
   Starlette; the thin `mcp_discovery()` handler may take precedence over the
   full `discovery()` that fires background RAG indexing. Verify in production
   and consolidate if needed.

5. **In-memory vector store is not distributed** — `TQVectorStore` is
   per-process in-memory. In a multi-worker deployment (e.g. `gunicorn -w 4`),
   each worker has its own store. Searches work correctly (lazy load from 8DNA
   on each worker), but memory usage is multiplied by the worker count.

6. **Max MCP result payload** — MCP tools return full chunk texts. For large
   documents with many chunks, `rag.document_list` may return a payload
   exceeding MCP tool output limits. Use `limit` parameter to paginate.

---

*Generated 2026-03-27 — see [CHANGELOG.md](../CHANGELOG.md) v0.4.3 for the full change list.*
