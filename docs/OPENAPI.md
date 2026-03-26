# OpenAPI — HTTP API reference

The AgentStack **REST** surface is described with **OpenAPI**. Use it to explore paths, parameters, request bodies, and response schemas.

**Production (cloud):**

| Resource | URL |
|----------|-----|
| **Swagger UI** (try requests) | [https://agentstack.tech/docs](https://agentstack.tech/docs) |
| **ReDoc** (readable reference) | [https://agentstack.tech/redoc](https://agentstack.tech/redoc) |
| **OpenAPI JSON** (machine-readable schema) | [https://agentstack.tech/openapi.json](https://agentstack.tech/openapi.json) |

**Self-hosted:** use the same paths on your deployment’s base URL, for example `https://your-api.example.com/docs` and `https://your-api.example.com/openapi.json`.

**Navigation tips:** In Swagger, endpoints are grouped by **tags** (e.g. **DataAccess**, **Projects**, **Sandbox** — exact names match your build). Use the filter box to jump to `/api/data-access`, `/api/sandbox`, etc.

**Related docs:** [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) (FAP) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) (sandboxes) · [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) (project/user data).
