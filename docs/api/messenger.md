# Messenger API (REST) — overview

Messenger-heavy workflows combine:

- **Streaming / delta pulls** — HTTP endpoints documented under **Social** tags in [OPENAPI.md](../OPENAPI.md) / Swagger.
- **MCP** — `social.chat.*` actions ([SOCIAL_API_REFERENCE.md](../messenger/SOCIAL_API_REFERENCE.md)).

Always authorize with your project-scoped token. Exact paths evolve — **`openapi.json`** on `agentstack.tech` is authoritative.

See also [messenger/README.md](../messenger/README.md).
