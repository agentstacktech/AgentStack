# OpenAPI — HTTP API reference

The AgentStack **REST compatibility** surface (many `/api/*` paths) is described with **OpenAPI**. **AI-oriented semantics** are primarily **`GET /mcp/actions`** and `POST /mcp` (`agentstack.execute`), plus 8DNA KV and Protein commands — see the main repo **[docs/API_CHANNELS_AND_PROTOCOLS.md](../../docs/API_CHANNELS_AND_PROTOCOLS.md)**.

On **agentstack.tech**, Swagger is **not** at `/docs` on the site root — nginx exposes the backend Swagger UI at **`/swagger`** (see `deploy/vps/nginx.conf`: proxy to backend `/docs`).

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

[ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) (FAP) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) (sandboxes) · [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) (RAG) · [ECOSYSTEM_API_IMPLEMENTATION.md](../../docs/archive/ECOSYSTEM_API_IMPLEMENTATION.md) (project/user data).
