# AgentStack — Documentation

[![platform](https://img.shields.io/badge/platform-v0.4.13-blue)](https://agentstack.tech)
[![MCP actions](https://img.shields.io/badge/MCP-332%20actions-8B5CF6)](https://agentstack.tech/mcp/actions)

This repository contains the **public** documentation for **AgentStack** (English, user- and integrator-facing): web product, MCP, plugins, REST APIs, **RAG**, sandboxes, subscriptions, and examples.

**If you use the website (dashboard):** start with [docs/USER_FEATURES_GUIDE.md](docs/USER_FEATURES_GUIDE.md) — RAG, sandboxes, field access, subscriptions, dual-shell surfaces, and dashboard modules in plain language.

**If you build your own product (app, site, client):** [docs/BUILD_YOUR_PRODUCT.md](docs/BUILD_YOUR_PRODUCT.md) — golden paths, `@agentstack/sdk`, optional [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter).

**If you integrate AI or automate:** [docs/MCP_AND_ECOSYSTEM.md](docs/MCP_AND_ECOSYSTEM.md) — MCP (**332** catalog actions — [docs/MCP_SCALE.md](docs/MCP_SCALE.md)), REST, plugins, SDK.

**REST / OpenAPI:** [docs/OPENAPI.md](docs/OPENAPI.md) — [Swagger UI](https://agentstack.tech/swagger) · [openapi.json](https://agentstack.tech/openapi.json) · [API overview](https://agentstack.tech/api-docs).

**npm SDK:** [@agentstack/sdk](https://www.npmjs.com/package/@agentstack/sdk) · [docs/sdk/](docs/sdk/)

**Full index:** [docs/README.md](docs/README.md) · **What's new:** [docs/WHATS_NEW.md](docs/WHATS_NEW.md)

---

## Open source kit (new projects)

| Repo | Branch | Notes |
|------|--------|--------|
| [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter) | `main` | Map-first install: `npx @agentstack/genetic-ai-starter init` — philosophy, `AI_NAVIGATION_MAP`, Cursor rules. SoT lives in [AgentStack](https://github.com/agentstacktech/AgentStack) `/genetic-ai-starter/`. |

---

## Plugins (separate repositories)

| Plugin | Notes |
|--------|--------|
| [cursor-plugin](https://github.com/agentstacktech/cursor-plugin) | **v0.4.9+** — 5-layer architecture (rules, skills, commands, agents, hooks); **OAuth 2.1 device code** flow for activation |
| [claude-plugin](https://github.com/agentstacktech/claude-plugin) | Claude Desktop / API installers |
| [gpt-plugin](https://github.com/agentstacktech/gpt-plugin) | ChatGPT / OpenAI ecosystem |
| [vscode-plugin](https://github.com/agentstacktech/vscode-plugin) | VS Code marketplace distribution |

---

## Product

- **Site:** [agentstack.tech](https://agentstack.tech)
- **GitHub org:** [github.com/agentstacktech](https://github.com/agentstacktech)
