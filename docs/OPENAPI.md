# OpenAPI — HTTP API reference

The AgentStack **REST compatibility** surface (many `/api/*` paths) is described with **OpenAPI**. **AI-oriented semantics** are primarily **`GET /mcp/actions`** and `POST /mcp` (`agentstack.execute`), plus 8DNA KV and Protein commands — see **[architecture/API_CHANNELS.md](architecture/API_CHANNELS.md)**.

On **agentstack.tech**, interactive docs are at **`/swagger`** (Swagger UI) and **`/api-docs`** (grouped overview).

## Production (agentstack.tech)

| Resource | URL |
|----------|-----|
| **Swagger UI** (try requests) | [https://agentstack.tech/swagger](https://agentstack.tech/swagger) |
| **OpenAPI JSON** (machine-readable schema) | [https://agentstack.tech/openapi.json](https://agentstack.tech/openapi.json) |
| **API overview page** (SPA — grouped endpoints, links to Swagger) | [https://agentstack.tech/api-docs](https://agentstack.tech/api-docs) |

## Self-hosted (same nginx pattern)

Use the same paths on your domain: **`/swagger`**, **`/openapi.json`**, and the frontend route **`/api-docs`** if you ship the same SPA.

## Navigation tips

In **Swagger**, tags are ordered with **unified access** first (**MCP**, **8DNA API**, **Commands**), then domain **REST compatibility** tags. **Redoc** may show **x-tagGroups** (“Unified access”, “Field access policy”). Use the filter box to jump to `/mcp`, `/api/dna/data`, `/api/commands`, or domain routes.

## Related docs

[FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md) (FAP) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) (sandboxes) · [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) (RAG) · [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) (project/user data).
