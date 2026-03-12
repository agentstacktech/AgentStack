# MCP and AgentStack Ecosystem — Index

Single entry point for MCP (Model Context Protocol) documentation, plugins, ecosystem API, and usage examples. Official repo: [https://github.com/agentstacktech/AgentStack](https://github.com/agentstacktech/AgentStack).

---

## MCP (Model Context Protocol)

- **[MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md)** — MCP doc index: quick start ([MCP_QUICKSTART](MCP_QUICKSTART.md)), overview and API ([MCP_OVERVIEW](MCP_OVERVIEW.md)), tools reference ([MCP_TOOLS](MCP_TOOLS.md)), features and examples ([MCP_FEATURES_EXAMPLES](MCP_FEATURES_EXAMPLES.md)). 60+ tools for Cursor, Claude, GPT, VS Code plugins.
- **[api/mcp.md](api/mcp.md)** — MCP configuration, authentication, public endpoints, examples for AI agents.

---

## Plugins

- **[plugins/README.md](plugins/README.md)** — Plugins index: Cursor, Claude Code, GPT (OpenAI), VS Code. Getting started, API key, Quick Start per platform. Plugin code on GitHub: [cursor-plugin](https://github.com/agentstacktech/cursor-plugin), [claude-plugin](https://github.com/agentstacktech/claude-plugin), [gpt-plugin](https://github.com/agentstacktech/gpt-plugin), [vscode-plugin](https://github.com/agentstacktech/vscode-plugin).
- **[plugins/CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md)** — Capability map for AI: which domain and which tools to use for a user request (Projects, 8DNA, Rules, Buffs, Payments, Auth, etc.).

---

## Ecosystem data

- **[ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md)** — Working with data: existing endpoints (no versioned paths), quick start, example of mobile game data storage (user.data.game.progress). Project and user data: `/api/projects/.../data`, `/api/dna/data`, MCP.
- **[architecture/DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md)** — Data store (project.data, user.data), key-value API **GET/POST /api/dna/data**, 8DNA usage.

---

## Usage examples

- **[examples/mcp_complex_projects.md](examples/mcp_complex_projects.md)** — Complex scenarios: SaaS with subscriptions, game with monetization, marketplace with promos, education platform with trials, enterprise with analytics. Synergy of Buffs, Projects, Payments, Scheduler, Logic Engine.
- **[examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md)** — Buff workflows (trials, renewal, cancellation).
- **[examples/mcp_buffs_temporary.md](examples/mcp_buffs_temporary.md)** — Temporary effects (trials, promos).
- **[examples/mcp_buffs_persistent.md](examples/mcp_buffs_persistent.md)** — Persistent effects (subscriptions, one-time purchases).

---

**Quick links for plugins:** [MCP_SERVER_CAPABILITIES](MCP_SERVER_CAPABILITIES.md) · [Plugins index](plugins/README.md) · [CONTEXT_FOR_AI](plugins/CONTEXT_FOR_AI.md).
