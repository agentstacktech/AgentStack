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

## GPT (OpenAI) — GPT Actions + MCP Connector

- **Integration model A (Custom GPT):** artifacts for **GPT Actions**. User creates a **Custom GPT** and attaches OpenAPI 3.1 schema + instructions.
- **Integration model B (Developer Mode):** native **MCP Connector** in ChatGPT — URL `https://agentstack.tech/mcp`, OAuth well-known or API key. Template: `provided_plugins/gpt-plugin/templates/chatgpt-mcp-connector.template.json`. Map: [MCP_CHATGPT_GEMINI_GUIDE_RU.md](MCP_CHATGPT_GEMINI_GUIDE_RU.md).
- **Manifest:** OpenAPI 3.1 schema (`provided_plugins/gpt-plugin/openapi/agentstack-mcp.yaml`) + reference instructions (`GPT_INSTRUCTIONS.md`). Install = create Custom GPT per `GPT_QUICKSTART.md`.
- **MCP:** same endpoint `https://agentstack.tech/mcp`; auth — API Key in header `X-API-Key` (set in Custom GPT → Action → Authentication).
- **OAuth (optional):** Custom GPT can use OAuth2 (Authorization Code) with AgentStack as IdP:
  - Authorization URL: `https://agentstack.tech/api/oauth2/authorize`
  - Token URL: `https://agentstack.tech/api/oauth2/token`
- **More:** artifact layout in `provided_plugins/gpt-plugin/ARTIFACTS.md`; quick start in `provided_plugins/gpt-plugin/GPT_QUICKSTART.md`.

## Gemini CLI — remote MCP client

- **Integration model:** Gemini CLI is an MCP **host** (not a marketplace plugin). User runs `gemini mcp add --transport http https://agentstack.tech/mcp`.
- **Manifest:** docs-only folder `provided_plugins/gemini-cli/` with `GEMINI_QUICKSTART.md`.
- **MCP:** same endpoint; auth — `X-API-Key` header or `/mcp auth` OAuth flow.
- **Gemini API:** Managed Agents accept `mcp_server` tool in Interactions API — see [MCP_CHATGPT_GEMINI_GUIDE_RU.md](MCP_CHATGPT_GEMINI_GUIDE_RU.md) §5.2.
- **No Gemini web chat connector** (as of 2026-08) — use CLI or API.

---

## Component comparison

| Component | Cursor | Claude Code | GPT (OpenAI) | VS Code |
|-----------|--------|-------------|--------------|---------|
| **Manifest** | `.cursor-plugin/plugin.json` | `.claude-plugin/plugin.json` | OpenAPI 3.1 schema + GPT_INSTRUCTIONS.md | `package.json` + `contributes.mcpServerDefinitionProviders` |
| **Install** | Copy plugin + MCP config | Install plugin + `claude mcp add` | Create Custom GPT, paste schema and instructions | Marketplace/VSIX + one-time API key entry |
| **MCP config** | `mcp.json` (HTTP: `type`, `baseUrl`, `headers`) | HTTP via user setup (see below) | API Key or OAuth in Action settings | HTTP via extension (auto-registration) |
| **Skills** | `plugins/agentstack/skills/*/SKILL.md` — **27** domain routers (25 mirrored to Claude/VS Code; `backend` + `solana` Cursor-only) | **25** gen3 mirrors via `sync-claude-skill-stubs.mjs` | No skill tree; `GPT_INSTRUCTIONS.md` from `agentstack-backend` router (`sync-gpt-instructions.mjs`) | **25** gen3 mirrors via `sync-vscode-skill-stubs.mjs` |
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

## Cursor gen3 domain parity (0.4.18)

| Layer | Cursor (SoT) | Claude | VS Code | GPT / Gemini |
|-------|--------------|--------|---------|--------------|
| Skills | `provided_plugins/cursor-plugin/plugins/agentstack/skills/` | `claude-plugin/skills/` | `vscode-plugin/skills/` | Instructions only (`gpt-plugin/instructions/`) |
| Sync | — | `sync-claude-skill-stubs.mjs` | `sync-vscode-skill-stubs.mjs` | `sync-gpt-instructions.mjs` |
| Parity gate | `audit-cursor-plugin` | `check-claude-skills-parity` + stub `--check` | `check-vscode-skills-parity` + stub `--check` | OpenAPI + instructions drift |
| Skip mirror | `agentstack-backend`, `solana` | same | same | backend router feeds GPT autogen block |

All **25** mirrored gen3 domains (data, logic, auth-rbac, commerce, CRM, agentnet, hosting, support, messenger, …) are synced with canonical folder names. Legacy gen1 folders (`agentstack-8dna`, `agentstack-payments`, …) are **retired** — `sync-*-skill-stubs.mjs` prunes them when canonical stubs exist.

Hooks and Device Code install are **Cursor-only**. Claude uses `claude mcp add` per [MCP_QUICKSTART](https://github.com/agentstacktech/claude-plugin/blob/master/MCP_QUICKSTART.md).

---

## What is reused

- **Skills** text and structure — sync via `sync-claude-skill-stubs.mjs` / `sync-vscode-skill-stubs.mjs` from `provided_plugins/cursor-plugin/plugins/agentstack/skills/` (gen3 decision-first names).
- **GPT:** same MCP endpoint and API key; key acquisition text reused from MCP_QUICKSTART; Custom GPT instructions reference MCP_CAPABILITY_MATRIX.
- Production MCP URL: `https://agentstack.tech/mcp`.
- Documentation: links to MCP_CAPABILITY_MATRIX, 8DNA, ecosystem without duplication.

---

## Skills: syncing Cursor, Claude, and VS Code

- **Source of truth:** `provided_plugins/cursor-plugin/plugins/agentstack/skills/` (gen3).
- **Claude:** `node provided_plugins/scripts/sync-claude-skill-stubs.mjs` → `claude-plugin/skills/`; replace "Cursor" with "Claude Code" (automated).
- **VS Code:** `node provided_plugins/scripts/sync-vscode-skill-stubs.mjs` → `vscode-plugin/skills/`; replace "Cursor" with "VS Code" (automated).
- **GPT:** no skill tree — `sync-gpt-instructions.mjs` extracts router table from `agentstack-backend/SKILL.md`.
- **Prune:** sync scripts remove legacy gen1 alias folders when canonical gen3 stubs exist (`--prune-legacy` on demand).
- **CI:** `npm run audit:agentstack-dx-plane` runs stub `--check` for Claude + VS Code; `validate-all-plugins.mjs` asserts full gen3 mirror set.
- **Links:** References to MCP_QUICKSTART and README point to each plugin root. Repo links (capability matrix, philosophy) stay shared.
- **Versioning:** when changing skills, update CHANGELOG in affected plugins. See [SKILLS_AUTHORING_GUIDE.md](SKILLS_AUTHORING_GUIDE.md) and [CLAUDE_SKILLS_SYNC_CHECKLIST.md](CLAUDE_SKILLS_SYNC_CHECKLIST.md).

---

**Genetic code (reference):** `docs.plugins.claude_vs_cursor_plugin.gen2`
