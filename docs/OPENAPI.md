# OpenAPI — HTTP API reference

The AgentStack **REST** API is described with **OpenAPI**. On **agentstack.tech**, Swagger is **not** at `/docs` on the site root — nginx exposes the backend Swagger UI at **`/swagger`** (see `deploy/vps/nginx.conf`: proxy to backend `/docs`).

## Production (agentstack.tech)

| Resource | URL |
|----------|-----|
| **Swagger UI** (try requests) | [https://agentstack.tech/swagger](https://agentstack.tech/swagger) |
| **OpenAPI JSON** (machine-readable schema) | [https://agentstack.tech/openapi.json](https://agentstack.tech/openapi.json) |
| **API overview page** (SPA — grouped endpoints, links to Swagger) | [https://agentstack.tech/api-docs](https://agentstack.tech/api-docs) |

## Self-hosted (same nginx pattern)

Use the same paths on your domain: **`/swagger`**, **`/openapi.json`**, and the frontend route **`/api-docs`** if you ship the same SPA.

## Navigation tips

In **Swagger**, endpoints are grouped by **tags** (e.g. **DataAccess**, **Projects**, **Sandbox**, **RAG** — exact names match your build). Use the filter box to jump to `/api/data-access`, `/api/sandbox`, `/api/rag`, etc.

## Related docs

[ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) (FAP) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) (sandboxes) · [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) (RAG) · [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) (project/user data).
