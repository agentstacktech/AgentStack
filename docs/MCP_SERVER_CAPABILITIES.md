# MCP Server — Capabilities Overview

Single index for MCP (Model Context Protocol) documentation for AgentStack. MCP is available in the cloud at **[agentstack.tech](https://agentstack.tech)** — one tool **agentstack.execute** with batched steps, 60+ actions. Base URL: `https://agentstack.tech/mcp`. See [MCP_CAPABILITY_MAP.md](MCP_CAPABILITY_MAP.md) for capabilities and metrics; [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) for all flows and step-by-step synergies.

---

## Documents (split for convenience)

| Document | Contents |
|----------|----------|
| **[OPENAPI.md](OPENAPI.md)** | HTTP REST: [Swagger UI](https://agentstack.tech/docs), [ReDoc](https://agentstack.tech/redoc), [openapi.json](https://agentstack.tech/openapi.json). |
| **[MCP_QUICKSTART.md](MCP_QUICKSTART.md)** | Quick start, setup in Cursor, getting an API key, debugging, example commands. |
| **[MCP_OVERVIEW.md](MCP_OVERVIEW.md)** | MCP overview, architecture, API endpoints (GET /mcp/discovery, GET /mcp/actions, POST /mcp with steps). |
| **[MCP_TOOLS.md](MCP_TOOLS.md)** | Full tool reference by category: Auth, Logic, Payments, Projects, Scheduler, Analytics, API Keys, Rules, Webhooks, Notifications, Wallets, Buffs, Workflows. |
| **[MCP_FEATURES_EXAMPLES.md](MCP_FEATURES_EXAMPLES.md)** | Implementation details (anonymous projects, attach to user, subscriptions), curl examples, summary stats. |
| **[SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md)** | Sandboxes: `/api/sandbox/*`, fork, checkpoints, A/B, canary, segments, hooks. |
| **[ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md)** | L1/L2/L3 access, Field Access Policy, `/api/data-access/*`, MCP `data_access.*`. |

---

## Quick links

- **HTTP / OpenAPI:** [OPENAPI.md](OPENAPI.md) — [Swagger](https://agentstack.tech/docs) · [ReDoc](https://agentstack.tech/redoc) · [openapi.json](https://agentstack.tech/openapi.json)
- **Configuration and authentication:** [api/mcp.md](api/mcp.md)
- **Ecosystem index:** [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md)
- **Sandbox & REST:** [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) · **Access & FAP:** [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) · [api/data-access.md](api/data-access.md)
- **Capability map for AI:** [plugins/CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md) · **Capabilities and metrics:** [MCP_CAPABILITY_MAP.md](MCP_CAPABILITY_MAP.md)
- **Scenario examples:** [examples/mcp_complex_projects.md](examples/mcp_complex_projects.md), [examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md)
