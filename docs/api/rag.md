# RAG Platform API — REST surface

**Base path:** `/api/rag/*` (authenticated; project-scoped like other ecosystem APIs).

**SDK:** `@agentstack/sdk/rag` (`sdk.rag.gen1`) — `AgentRag` on `getSDKInstance().rag`.

## Health

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/api/rag/health` | Persistence mode + collection/chunk stats (`?home_project_id=` optional) |

## Collections

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/collections` | Create collection (`scope`: `project` \| `user`, `home_project_id` optional) |
| `GET` | `/api/rag/collections` | List project + personal KB (`?home_project_id=` optional) |
| `DELETE` | `/api/rag/collections/{collection_id}` | Delete collection (cascade) |

## Documents (chunks)

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/collections/{collection_id}/documents` | Ingest document (auto-chunked) |
| `POST` | `/api/rag/collections/{collection_id}/documents/batch` | Batch ingest (≥12 docs → work queue) |
| `POST` | `/api/rag/collections/{collection_id}/ingest-from-storage` | Ingest from Storage file card |
| `GET` | `/api/rag/collections/{collection_id}/documents` | List chunks |
| `DELETE` | `/api/rag/collections/{collection_id}/documents/{doc_id}` | Remove chunk |
| `POST` | `/api/rag/collections/{collection_id}/search` | Semantic (+ hybrid) search |
| `GET` | `/api/rag/collections/{collection_id}/export` | Export collection documents |

## Session memory

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/rag/memory/{session_id}/add` | Append conversation turn |
| `GET` | `/api/rag/memory/{session_id}` | Recent turns |
| `POST` | `/api/rag/memory/{session_id}/search` | Semantic search over memory |

---

**Full guide (TurboQuant, storage model, MCP `rag.*` actions, tiers, dashboard):** [RAG_PLATFORM_GUIDE.md](../RAG_PLATFORM_GUIDE.md)

**OpenAPI:** [Swagger UI](https://agentstack.tech/swagger) → tag **RAG** · [OPENAPI.md](../OPENAPI.md) · [openapi.json](https://agentstack.tech/openapi.json)
