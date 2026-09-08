# AgentStack Plugins — Index

**Purpose:** Single entry point for AgentStack plugin documentation (Decomposition: one index; Elegant Minimalism: no duplication — details by link).

**For AI agents:** Start with [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) and [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md). Universal client matrix: [MCP_CLIENT_COMPATIBILITY_MATRIX.md](MCP_CLIENT_COMPATIBILITY_MATRIX.md). Browser prompts: [MCP_CHAT_COMMAND_COOKBOOK.md](MCP_CHAT_COMMAND_COOKBOOK.md). Plugin source repos: [agentstacktech/cursor-plugin](https://github.com/agentstacktech/cursor-plugin), [agentstacktech/claude-plugin](https://github.com/agentstacktech/claude-plugin), [agentstacktech/vscode-plugin](https://github.com/agentstacktech/vscode-plugin), [agentstacktech/gpt-plugin](https://github.com/agentstacktech/gpt-plugin), [agentstacktech/gemini-plugin](https://github.com/agentstacktech/gemini-plugin) (sync from monorepo).

**Philosophy:** See [AGENTSTACK_PLUGIN_PHILOSOPHY.md](AGENTSTACK_PLUGIN_PHILOSOPHY.md).

---

## Plugins by platform

All plugins are published as separate GitHub repositories (see table below). On GitHub the docs repo is [agentstacktech/AgentStack](https://github.com/agentstacktech/AgentStack).

| Platform     | Folder | GitHub | Summary |
|-------------|--------|--------|---------|
| **Cursor**  | [cursor-plugin](https://github.com/agentstacktech/cursor-plugin) | [agentstacktech/cursor-plugin](https://github.com/agentstacktech/cursor-plugin) | **v0.4.18, gen3** — Plugin MCP via `mcpServers: "./mcp.json"` + Connect (Device Code); single `tools/list` tool; flow map [MCP_DEDUPE_FLOW.md](MCP_DEDUPE_FLOW.md). |
| **Claude Code** | [claude-plugin](https://github.com/agentstacktech/claude-plugin) | [agentstacktech/claude-plugin](https://github.com/agentstacktech/claude-plugin) | Claude Code plugin: Skills, manifest `.claude-plugin/plugin.json`. MCP: `claude mcp add --transport http`. |
| **GPT (OpenAI)** | [gpt-plugin](https://github.com/agentstacktech/gpt-plugin) | [agentstacktech/gpt-plugin](https://github.com/agentstacktech/gpt-plugin) | GPT Actions + ChatGPT MCP Connector — [GPT quick start](https://github.com/agentstacktech/gpt-plugin/blob/main/GPT_QUICKSTART.md). |
| **Gemini** | [gemini-plugin](https://github.com/agentstacktech/gemini-plugin) | [agentstacktech/gemini-plugin](https://github.com/agentstacktech/gemini-plugin) | CLI + Spark Connected Apps — [GEMINI quick start](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_CLI_QUICKSTART.md). |
| **VS Code** | [vscode-plugin](https://github.com/agentstacktech/vscode-plugin) | [agentstacktech/vscode-plugin](https://github.com/agentstacktech/vscode-plugin) | VS Code extension: MCP registered by extension; one-time API key entry (SecretStorage). |

---

## Getting started

**Flow:** create an anonymous project (no account) → get API key or OAuth Bearer → add it in the plugin → use `agentstack.execute` in chat with the live action catalog. Endpoint `GET /mcp/actions` is the current discovery source, and `projects.create_project_anonymous` works before full plugin configuration.

1. **Choose a plugin** for your environment: Cursor, Claude Code, VS Code, or GPT (Custom GPT in ChatGPT).
2. **Get an API key** — no signup (anonymous project) or from [AgentStack](https://agentstack.tech) in project settings.
3. **Follow Quick Start** for your plugin (links below) — connect in a few minutes.

**What plugins provide:** one MCP endpoint (`https://agentstack.tech/mcp`) and **<!-- stats:total_actions -->568<!-- /stats:total_actions --> catalog actions** for projects, 8DNA data, Rules Engine, payments, Buffs, auth, agents, **hosted static sites** (`hosting.*`), **project files** (`storage.*`), support, messenger, and more. Scale facts: [PLATFORM_SCALE.md](../publication/PLATFORM_SCALE.md). Full list: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

---

## Key documents

| Document | Content |
|----------|---------|
| [Cursor plugin audit (maintainers)](https://github.com/agentstacktech/agentstack/blob/main/docs/plugins/CURSOR_PLUGIN_AUDIT_2026-07.md) | Cursor marketplace ship audit (P0–P2 gap register) |
| [PLUGIN_VERSION_POLICY.md](PLUGIN_VERSION_POLICY.md) | Plugin semver: **do not bump** without Lance’s explicit order (see policy doc) |
| [MCP_CHATGPT_GEMINI_GUIDE_RU.md](MCP_CHATGPT_GEMINI_GUIDE_RU.md) | **ChatGPT + Gemini:** integration map, auth matrix, troubleshooting |
| [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) | AgentStack capability map for AI (domains, when to use which tool); for GPT, VS Code, etc. |
| [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) | Stable public link to the full MCP action catalogue (source: `GET /mcp/actions`). |
| [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md) | Comparison of all four plugins: manifest, install, MCP config, Skills/Rules. |
| [AGENTSTACK_PLUGIN_PHILOSOPHY.md](AGENTSTACK_PLUGIN_PHILOSOPHY.md) | Plugin validation via PHILOSOPHY_INDEX (Creation over Conflict, Decomposition, Elegant Minimalism, 8DNA, Time) + v0.4.9 5-layer model. |
| [../MCP_QUICKSTART.md](../MCP_QUICKSTART.md) | Lean verify + first call; full install → [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md) |
| [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md) | **Install all clients** — Cursor, Claude, VS Code, ChatGPT/Gemini, copy-paste configs |
| [MCP_BROWSER_QUICKSTART.md](MCP_BROWSER_QUICKSTART.md) | ChatGPT Custom GPT + Gemini Spark (browser) |

---

## Publisher copy

Ready-made titles, descriptions, and keywords for marketplaces live in each plugin repository README (Cursor, VS Code, Claude, GPT).

## Post-release checklists (for maintainers)

Checklists ship inside each plugin repo (`POST_RELEASE_CHECKLIST.md` or equivalent) — not duplicated in this public docs mirror.

---

## Quick links by plugin

- **Cursor:** [cursor-plugin README](https://github.com/agentstacktech/cursor-plugin#readme) · [MCP quick start](https://github.com/agentstacktech/cursor-plugin/blob/main/MCP_QUICKSTART.md)
- **Claude:** [claude-plugin README](https://github.com/agentstacktech/claude-plugin#readme) · [MCP quick start](https://github.com/agentstacktech/claude-plugin/blob/main/MCP_QUICKSTART.md)
- **GPT:** [gpt-plugin README](https://github.com/agentstacktech/gpt-plugin#readme) · [GPT quick start](https://github.com/agentstacktech/gpt-plugin/blob/main/GPT_QUICKSTART.md) · [ChatGPT + Gemini map](MCP_CHATGPT_GEMINI_GUIDE_RU.md)
- **Gemini CLI:** [GEMINI quick start](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_CLI_QUICKSTART.md)
- **VS Code:** [vscode-plugin README](https://github.com/agentstacktech/vscode-plugin#readme) · [MCP quick start](https://github.com/agentstacktech/vscode-plugin/blob/main/MCP_QUICKSTART.md)

Full MCP tools list: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).  
Data store (how to use the “database”): [DNA_KEY_VALUE_API.md](../architecture/DNA_KEY_VALUE_API.md) (key-value API: project.data.*, user.data.*).

---

## FAQ

| Question | Answer |
|----------|--------|
| **Where is the full tool list and parameters?** | [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) |
| **How do I know which tool to use for a task?** | [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) — domain map (Projects, Payments, Rules, Buffs, etc.) |
| **How do I store and read project/user data?** | Key-value API: `project.data.*`, `user.data.*` — see [DNA_KEY_VALUE_API.md](../architecture/DNA_KEY_VALUE_API.md) |


<!-- BEGIN:PLUGIN_INVENTORY -->
| Artifact | Count |
|----------|------:|
| Cursor skills | 27 |
| Cursor commands | 15 |
| Cursor agents | 3 |
| MCP catalog actions | 568 |
| MCP domains | 48 |
| Platform version | 0.4.18 |
<!-- END:PLUGIN_INVENTORY -->
