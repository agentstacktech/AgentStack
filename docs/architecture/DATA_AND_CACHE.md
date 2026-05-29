# Data and cache (high level)

- **8DNA** stores project and user JSON documents — primary app state
- **Field Access Policy** masks fields per role on read
- **Client snapshots** (`sdk.protocol`) cache JSON for fast UI; invalidate after writes
- **RAG** maintains separate collections and vectors — [RAG_PLATFORM_GUIDE.md](../RAG_PLATFORM_GUIDE.md)

Integrators do not need separate cache products for basic CRUD — use SDK snapshot helpers and TanStack Query on the client.
