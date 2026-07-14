# Plugins: Claude, Cursor, GPT, VS Code — Differences and choices

**Version:** 0.4  
**Date:** 2026-06-28  
**Platform:** 0.4.14 · MCP catalog via `GET /mcp/actions` (live discovery; see capability matrix in repo docs)
**Context:** AgentStack plugins for **Claude Code**, **Cursor**, **GPT (OpenAI)**, and **VS Code**; alignment and philosophy. Plugin index: [docs/plugins/README.md](README.md).

---

## Where they live

Plugin source on GitHub: [cursor-plugin](https://github.com/agentstacktech/cursor-plugin), [claude-plugin](https://github.com/agentstacktech/claude-plugin), [gpt-plugin](https://github.com/agentstacktech/gpt-plugin), [vscode-plugin](https://github.com/agentstacktech/vscode-plugin).

- **Cursor:** cursor-plugin
- **Claude:** claude-plugin
- **GPT (OpenAI):** gpt-plugin
- **VS Code:** vscode-plugin

One plugin — one artifact (Decomposition). Shared MCP endpoint and ecosystem; manifests and configs differ by platform.

---

## GPT (OpenAI) — GPT Actions

- **Integration model:** not a "plugin package" but artifacts for **GPT Actions**. The user creates a **Custom GPT** in ChatGPT and attaches the OpenAPI 3.1 schema + instructions.
- **Manifest:** OpenAPI 3.1 schema (`provided_plugins/gpt-plugin/openapi/agentstack-mcp.yaml`) + reference instructions (`GPT_INSTRUCTIONS.md`). Install = create Custom GPT per `GPT_QUICKSTART.md`.
- **MCP:** same endpoint `https://agentstack.tech/mcp`; auth — API Key in header `X-API-Key` (set in Custom GPT → Action → Authentication).
- **OAuth (optional):** Custom GPT can use OAuth2 (Authorization Code) with AgentStack as IdP:
  - Authorization URL: `https://agentstack.tech/api/oauth2/authorize`
  - Token URL: `https://agentstack.tech/api/oauth2/token`
- **More:** artifact layout in `provided_plugins/gpt-plugin/ARTIFACTS.md`; quick start in `provided_plugins/gpt-plugin/GPT_QUICKSTART.md`.

---

## Component comparison

| Component | Cursor | Claude Code | GPT (OpenAI) | VS Code |
|-----------|--------|-------------|--------------|---------|
| **Manifest** | `.cursor-plugin/plugin.json` | `.claude-plugin/plugin.json` | OpenAPI 3.1 schema + GPT_INSTRUCTIONS.md | `package.json` + `contributes.mcpServerDefinitionProviders` |
| **Install** | Copy plugin + MCP config | Install plugin + `claude mcp add` | Create Custom GPT, paste schema and instructions | Marketplace/VSIX + one-time API key entry |
| **MCP config** | `mcp.json` (HTTP: `type`, `baseUrl`, `headers`) | HTTP via user setup (see below) | API Key or OAuth in Action settings | HTTP via extension (auto-registration) |
| **Skills** | `skills/*/SKILL.md` — **24** domain routers (incl. CRM, AgentNet, storefront, wallet, guidance) | **11** synced skills + uplift path for messenger/rag/sdk gaps | No equivalent; context in Custom GPT instructions | No equivalent; context in MCP and README |
| **Rules** | `rules/*.mdc` (Cursor-specific) | No equivalent; knowledge in Skills + doc links | No equivalent | No equivalent |

---

## MCP: HTTP in Claude Code

- In the Claude Code **plugin** (`.mcp.json`) official docs only describe running MCP via **command** (stdio). For **remote HTTP** servers the user configures MCP manually.
- **Recommended flow for AgentStack:** after installing the plugin the user runs once:
  ```bash
  claude mcp add --transport http agentstack https://agentstack.tech/mcp --header "X-API-Key: <YOUR_API_KEY>"
  ```
  Or configures MCP in Claude Code UI (if available) per MCP_QUICKSTART.md.

- **Summary:** we do not add `.mcp.json` with HTTP in `provided_plugins/claude-plugin/` (HTTP format in plugin bundle is not fixed); all MCP connection steps are described in `MCP_QUICKSTART.md` and README (Elegant Minimalism: minimal steps, one API key).

### Claude and OAuth

- Current recommended path for Claude Code is API key (`X-API-Key`) for MCP HTTP.
- If the Claude platform/MCP catalog requires OAuth, AgentStack already provides standard OAuth2 endpoints (`/api/oauth2/authorize`, `/api/oauth2/token`) without a separate "for Claude" implementation.

---

## Cursor gen3 domain parity (2026-06)

| Domain | Cursor skill | Claude (sync target) |
|--------|--------------|----------------------|
| Meta-router | `agentstack-backend` | Same pattern |
| Data / 8DNA | `agentstack-data` | `agentstack-data` |
| CRM | `agentstack-crm` | `agentstack-crm` (stub) |
| AgentNet economy | `agentstack-agentnet` | `agentstack-agentnet` (stub) |
| Storefront studio | `agentstack-storefront-studio` | `agentstack-storefront-studio` (stub) |
| Project wallet | `agentstack-project-wallet` | `agentstack-project-wallet` (stub) |
| Platform guidance | `agentstack-guidance` | `agentstack-guidance` (stub) |
| Hosting | `agentstack-hosting` | Add or merge in claude-plugin |
| Support | `agentstack-support` | Add or merge |
| Storage | `agentstack-storage` | Add or merge |
| Auth/RBAC | `agentstack-auth-rbac` | Existing claude skills |
| Logic / Commerce / RAG / Signals / Projects | matching `agentstack-*` | Partial parity today |
| Messenger / Integrations / Discovery | gen3 only on Cursor | Optional claude sync (11 WARN gaps without `--strict`) |

Hooks and Device Code install are **Cursor-only**. Claude uses `claude mcp add` per [MCP_QUICKSTART](https://github.com/agentstacktech/claude-plugin/blob/master/MCP_QUICKSTART.md).

---

## What is reused

- **Skills** text and structure — copy from `provided_plugins/cursor-plugin/skills/` (gen3 decision-first names: `agentstack-data`, `agentstack-logic`, …); replace "Cursor" with "Claude Code" in instructions.
- **GPT:** same MCP endpoint and API key; key acquisition text reused from MCP_QUICKSTART; Custom GPT instructions reference MCP_CAPABILITY_MATRIX.
- Production MCP URL: `https://agentstack.tech/mcp`.
- Documentation: links to MCP_CAPABILITY_MATRIX, 8DNA, ecosystem without duplication.

---

## Skills: syncing Cursor and Claude

- **Source of truth:** `provided_plugins/cursor-plugin/skills/` (gen3). Copy to `provided_plugins/claude-plugin/skills/` on release; retire gen1 folder names (`agentstack-8dna`, etc.).
- **Claude adaptation:** in copied SKILL.md replace "Cursor" with "Claude Code" in the body (e.g. "add MCP in Cursor" → "add MCP in Claude Code"). Frontmatter (name, description) unchanged.
- **Links in Claude version:** References to MCP_QUICKSTART and README point to artifacts in claude-plugin root (MCP_QUICKSTART.md, README.md — same plugin). Repo links (MCP_CAPABILITY_MATRIX, philosophy) stay shared.
- **Versioning:** when changing skills, update CHANGELOG in both plugins (Time Processes Philosophy). See also [SKILLS_AUTHORING_GUIDE.md](SKILLS_AUTHORING_GUIDE.md).

---

**Genetic code (reference):** `docs.plugins.claude_vs_cursor_plugin.gen2`
