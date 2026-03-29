# Plugins: Claude, Cursor, GPT, VS Code — Differences and choices

**Version:** 0.3  
**Date:** 2026-02-23  
**Context:** AgentStack plugins for **Claude Code**, **Cursor**, **GPT (OpenAI)**, and **VS Code** — same MCP endpoint, different packaging. Plugin index: [docs/plugins/README.md](README.md).

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
| **Skills** | `skills/*/SKILL.md` (8DNA, Projects, Rules Engine, Assets, RBAC, Buffs, Payments, Auth) | same format | No equivalent; context in Custom GPT instructions | No equivalent; context in MCP and README |
| **Rules** | `rules/*.mdc` (Cursor-specific) | No equivalent; knowledge in Skills + doc links | No equivalent | No equivalent |

---

## MCP: HTTP in Claude Code

- In the Claude Code **plugin** (`.mcp.json`) official docs only describe running MCP via **command** (stdio). For **remote HTTP** servers the user configures MCP manually.
- **Recommended flow for AgentStack:** after installing the plugin the user runs once:
  ```bash
  claude mcp add --transport http agentstack https://agentstack.tech/mcp --header "X-API-Key: <YOUR_API_KEY>"
  ```
  Or configures MCP in Claude Code UI (if available) per MCP_QUICKSTART.md.

- **Summary:** we do not add `.mcp.json` with HTTP in `provided_plugins/claude-plugin/` (HTTP format in plugin bundle is not fixed); all MCP connection steps are described in `MCP_QUICKSTART.md` and README (minimal steps, one API key).

### Claude and OAuth

- Current recommended path for Claude Code is API key (`X-API-Key`) for MCP HTTP.
- If the Claude platform/MCP catalog requires OAuth, AgentStack already provides standard OAuth2 endpoints (`/api/oauth2/authorize`, `/api/oauth2/token`) without a separate "for Claude" implementation.

---

## What is reused

- **Skills** text and structure (8DNA, Projects, Rules Engine, **Assets**, **RBAC**, **Buffs**, **Payments**, **Auth**) — we copy and adapt frontmatter if needed; replace "Cursor" with "Claude Code" in instructions.
- **GPT:** same MCP endpoint and API key; key acquisition text reused from MCP_QUICKSTART; Custom GPT instructions reference MCP_SERVER_CAPABILITIES.
- Production MCP URL: `https://agentstack.tech/mcp`.
- Documentation: links to MCP_SERVER_CAPABILITIES, 8DNA, ecosystem without duplication.

---

## Skills: syncing Cursor and Claude

- **Source of truth:** edits go in `provided_plugins/cursor-plugin/skills/`. On release or skill update, copy content to `provided_plugins/claude-plugin/skills/` (folders: agentstack-8dna, agentstack-projects, agentstack-rules-engine, agentstack-assets, agentstack-rbac, agentstack-buffs, agentstack-payments, agentstack-auth).
- **Claude adaptation:** in copied SKILL.md replace "Cursor" with "Claude Code" in the body (e.g. "add MCP in Cursor" → "add MCP in Claude Code"). Frontmatter (name, description) unchanged.
- **Links in Claude version:** References to MCP_QUICKSTART and README point to artifacts in claude-plugin root (MCP_QUICKSTART.md, README.md — same plugin). Links to **MCP_SERVER_CAPABILITIES** and other files under **docs/** stay shared from this repository.
- **Versioning:** when changing skills, update CHANGELOG in both plugins. See also [SKILLS_AUTHORING_GUIDE.md](SKILLS_AUTHORING_GUIDE.md).

---

**Genetic code (reference):** `docs.plugins.claude_vs_cursor_plugin.gen2`
