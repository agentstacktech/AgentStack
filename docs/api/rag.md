# RAG Platform API — REST surface

**Base path:** `/api/rag/*` (authenticated; project-scoped like other ecosystem APIs).

## Collections

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/collections` | Create collection (name, embedding provider, …) |
| `GET` | `/api/rag/collections` | List collections for the current project |
| `DELETE` | `/api/rag/collections/{collection_id}` | Delete collection (cascade) |

## Documents (chunks)

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/collections/{collection_id}/documents` | Ingest document (auto-chunked) |
| `GET` | `/api/rag/collections/{collection_id}/documents` | List chunks |
| `DELETE` | `/api/rag/collections/{collection_id}/documents/{doc_id}` | Remove chunk |
| `POST` | `/api/rag/collections/{collection_id}/search` | Semantic (+ hybrid) search |

## Session memory

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/memory/{session_id}/add` | Append conversation turn |
| `GET` | `/api/rag/memory/{session_id}` | Recent turns |
| `POST` | `/api/rag/memory/{session_id}/search` | Semantic search over memory |

---

**Full guide (TurboQuant, storage model, MCP `rag.*` actions, tiers, dashboard):** [RAG_PLATFORM_GUIDE.md](../RAG_PLATFORM_GUIDE.md)

**OpenAPI:** [Swagger UI](https://agentstack.tech/swagger) → tag **RAG** · [OPENAPI.md](../OPENAPI.md) · [openapi.json](https://agentstack.tech/openapi.json)
