# MCP and AgentStack Ecosystem — Index

Single entry point for MCP (Model Context Protocol) documentation, plugins, ecosystem API, and usage examples. Official repo: [https://github.com/agentstacktech/AgentStack](https://github.com/agentstacktech/AgentStack).

---

## HTTP API (OpenAPI)

Explore and try **REST** endpoints interactively: **[OPENAPI.md](OPENAPI.md)** — [Swagger UI](https://agentstack.tech/swagger) · [openapi.json](https://agentstack.tech/openapi.json) · [API overview](https://agentstack.tech/api-docs).

---

## MCP (Model Context Protocol)

- **[MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md)** — MCP doc index: quick start ([MCP_QUICKSTART](MCP_QUICKSTART.md)), overview and API ([MCP_OVERVIEW](MCP_OVERVIEW.md)), tools reference ([MCP_TOOLS](MCP_TOOLS.md)), features and examples ([MCP_FEATURES_EXAMPLES](MCP_FEATURES_EXAMPLES.md)). 60+ tools for Cursor, Claude, GPT, VS Code plugins.
- **[api/mcp.md](api/mcp.md)** — MCP configuration, authentication, public endpoints, examples for AI agents.

### MCP

- **Base URL:** `https://agentstack.tech/mcp`
- **Entrypoint:** `POST /mcp` — body: `{ "steps": [ { "id": "...", "action": "projects.get_project", "params": {...} } ], "options": { "stopOnError": true } }`
- **Action list for AI:** `GET /mcp/actions` — all available `action` values by domain (projects, buffs, auth, payments, logic, assets, etc.).
- **Discovery:** `GET /mcp/discovery` — protocol info and the single tool schema.
- **Capability map:** [CONTEXT_FOR_AI_MCP (monorepo)](https://github.com/agentstacktech/AgentStack/blob/main/docs/plugins/CONTEXT_FOR_AI_MCP.md) · [MCP_CAPABILITY_MAP (monorepo)](https://github.com/agentstacktech/AgentStack/blob/main/docs/MCP_CAPABILITY_MAP.md).

---

## Plugins

- **[plugins/README.md](plugins/README.md)** — Plugins index: Cursor, Claude Code, GPT (OpenAI), VS Code. Getting started, API key, Quick Start per platform. Plugin code on GitHub: [cursor-plugin](https://github.com/agentstacktech/cursor-plugin), [claude-plugin](https://github.com/agentstacktech/claude-plugin), [gpt-plugin](https://github.com/agentstacktech/gpt-plugin), [vscode-plugin](https://github.com/agentstacktech/vscode-plugin).
- **[plugins/CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md)** — Capability map for AI: which domain and which tools to use for a user request (Projects, 8DNA, Rules, Buffs, Payments, Auth, etc.).

---

## Ecosystem data

- **[ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md)** — Working with data: existing endpoints (no versioned paths), quick start, example of mobile game data storage (user.data.game.progress). Project and user data: `/api/projects/.../data`, `/api/dna/data`, MCP.
- **[architecture/DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md)** — Data store (project.data, user.data), key-value API **GET/POST /api/dna/data**, 8DNA usage.

---

## Sandboxes & access control

- **[SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md)** — Developer guide: environments, generations, fork/checkpoint/promote, A/B tests, canary rollout, segments, shadow writes, protected fields, REST `/api/sandbox/*`, subscription limits.
- **[ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md)** — **L1** API key `service_caps`, **L2** RBAC, **L3** Field Access Policy (FAP); global template; REST DataAccess summary; MCP `data_access.*` tools.

---

## Usage examples

- **[examples/mcp_complex_projects.md](examples/mcp_complex_projects.md)** — Complex scenarios: SaaS with subscriptions, game with monetization, marketplace with promos, education platform with trials, enterprise with analytics. Synergy of Buffs, Projects, Payments, Scheduler, Logic Engine.
- **[examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md)** — Buff workflows (trials, renewal, cancellation).
- **[examples/mcp_buffs_temporary.md](examples/mcp_buffs_temporary.md)** — Temporary effects (trials, promos).
- **[examples/mcp_buffs_persistent.md](examples/mcp_buffs_persistent.md)** — Persistent effects (subscriptions, one-time purchases).

---

**Quick links:** [OpenAPI](OPENAPI.md) · [MCP_SERVER_CAPABILITIES](MCP_SERVER_CAPABILITIES.md) · [Plugins index](plugins/README.md) · [CONTEXT_FOR_AI](plugins/CONTEXT_FOR_AI.md) · [CONTEXT_FOR_AI MCP (monorepo)](https://github.com/agentstacktech/AgentStack/blob/main/docs/plugins/CONTEXT_FOR_AI_MCP.md) · [Sandbox & environments](SANDBOX_AND_ENVIRONMENTS.md) · [Access & FAP](ACCESS_AND_FIELD_POLICY.md).
