# AgentStack Documentation

Public documentation for **AgentStack** (**English only**): web dashboard, MCP, plugins, REST APIs, **RAG**, sandboxes, access control, subscriptions, and examples.

---

## For people using the website

**Start here:** [USER_FEATURES_GUIDE.md](USER_FEATURES_GUIDE.md) — dashboard modules, **RAG**, sandboxes, field access, subscriptions, AI Builder note, links to detail guides.

Then, as needed: [subscription/SUBSCRIPTION_TIERS.md](subscription/SUBSCRIPTION_TIERS.md) · [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) · [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) · [SANDBOX_PLAYGROUND_GUIDE.md](SANDBOX_PLAYGROUND_GUIDE.md).

---

## For integrators and developers

**Ecosystem index:** [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md) — MCP, plugins, ecosystem data, **RAG**, sandboxes, examples.

**HTTP / REST:** [OPENAPI.md](OPENAPI.md) — [Swagger UI](https://agentstack.tech/swagger), [openapi.json](https://agentstack.tech/openapi.json), [API overview](https://agentstack.tech/api-docs).

---

## Full contents

| Section | Description |
|--------|--------------|
| [USER_FEATURES_GUIDE.md](USER_FEATURES_GUIDE.md) | **Users:** dashboard, RAG, sandboxes, access, subscriptions (plain language). |
| [OPENAPI.md](OPENAPI.md) | **OpenAPI:** Swagger (`/swagger`), `openapi.json`, `/api-docs`, self-hosted. |
| [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md) | Single entry point: MCP, Plugins, Ecosystem, **RAG**, Sandboxes, FAP, Examples. |
| [MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md) | MCP index: quick start, overview, [tools reference](MCP_TOOLS.md), features and examples. |
| [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) | **RAG platform:** collections, memory, hybrid search, REST `/api/rag/*`, MCP `rag.*`, tiers. |
| [SANDBOX_PLAYGROUND_GUIDE.md](SANDBOX_PLAYGROUND_GUIDE.md) | **Sandbox Playground:** flows, rollout patterns, limits (companion to SANDBOX_AND_ENVIRONMENTS). |
| [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) | **Three-layer access** (API key caps, RBAC, FAP). |
| [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md) | FAP policy format, ecosystem, triggers (detailed). |
| [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) | **Sandboxes:** fork, checkpoints, A/B, canary, promotion, segments, API. |
| [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) | Ecosystem API: data API, endpoints, usage. |
| [subscription/](subscription/) | **Tiers:** [SUBSCRIPTION_TIERS.md](subscription/SUBSCRIPTION_TIERS.md), [ANONYMOUS_TIER.md](subscription/ANONYMOUS_TIER.md). |
| [plugins/](plugins/) | Plugins index (Cursor, Claude, GPT, VS Code), comparison, CONTEXT_FOR_AI, Skills guide. |
| [examples/](examples/) | MCP usage examples: complex projects, buffs (workflows, temporary, persistent). |
| [architecture/](architecture/) | DNA Key-Value API (project.data, user.data). |
| [api/](api/) | [mcp.md](api/mcp.md), [rag.md](api/rag.md), [sandbox.md](api/sandbox.md), [data-access-api.md](api/data-access-api.md); all REST: [OPENAPI.md](OPENAPI.md). |

---

## Plugins (separate repositories)

- [cursor-plugin](https://github.com/agentstacktech/cursor-plugin)
- [claude-plugin](https://github.com/agentstacktech/claude-plugin)
- [gpt-plugin](https://github.com/agentstacktech/gpt-plugin)
- [vscode-plugin](https://github.com/agentstacktech/vscode-plugin)

Product: [agentstack.tech](https://agentstack.tech) · GitHub: [agentstacktech](https://github.com/agentstacktech)
