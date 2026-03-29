# AgentStack Plugins — Index

Plugins for Cursor, Claude Code, GPT (OpenAI), and VS Code: one MCP endpoint, **70+** actions (including **RAG**). Choose your platform and follow the Quick Start in each plugin repository.

---

## Plugins by platform

| Platform | Repository | Description |
|----------|------------|-------------|
| **Cursor** | [agentstacktech/cursor-plugin](https://github.com/agentstacktech/cursor-plugin) | Skills, Rules (.mdc), MCP config. |
| **Claude Code** | [agentstacktech/claude-plugin](https://github.com/agentstacktech/claude-plugin) | Skills, `claude mcp add --transport http`. |
| **GPT (OpenAI)** | [agentstacktech/gpt-plugin](https://github.com/agentstacktech/gpt-plugin) | GPT Actions (OpenAPI + instructions), Custom GPT. |
| **VS Code** | [agentstacktech/vscode-plugin](https://github.com/agentstacktech/vscode-plugin) | Extension: MCP + API key in SecretStorage. |

---

## Getting started

1. **Choose a plugin** for your environment (Cursor, Claude Code, VS Code, or GPT).
2. **Get an API key** — create an anonymous project (no account) or from [AgentStack](https://agentstack.tech) project settings.
3. **Follow the Quick Start** in the plugin repo (README, MCP_QUICKSTART, or GPT_QUICKSTART).

**What you get:** one MCP endpoint (`https://agentstack.tech/mcp`), **70+** tools: projects, 8DNA data, Rules Engine, payments, Buffs, RBAC, auth, scheduler, analytics, **RAG** (collections, documents, memory), webhooks, notifications, wallets. Full list: [MCP_SERVER_CAPABILITIES.md](../MCP_SERVER_CAPABILITIES.md).

**RAG & sandboxes (REST):** [RAG_PLATFORM_GUIDE.md](../RAG_PLATFORM_GUIDE.md) · [SANDBOX_PLAYGROUND_GUIDE.md](../SANDBOX_PLAYGROUND_GUIDE.md).

---

## Key documents

| Document | Description |
|----------|-------------|
| [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) | When to use which tool (domain map for Projects, Payments, Rules, Buffs, RAG, Auth, etc.). |
| [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md) | Comparison of all four plugins: setup, MCP config, Skills. |
| [SKILLS_AUTHORING_GUIDE.md](SKILLS_AUTHORING_GUIDE.md) | How to write and structure Skills for Cursor/Claude. |
| [CONTEXT_FOR_AI_MCP.md](CONTEXT_FOR_AI_MCP.md) | MCP-oriented capability map for agents. |

---

## Quick links to plugin READMEs

- [Cursor plugin](https://github.com/agentstacktech/cursor-plugin#readme)
- [Claude plugin](https://github.com/agentstacktech/claude-plugin#readme)
- [GPT plugin](https://github.com/agentstacktech/gpt-plugin#readme)
- [VS Code plugin](https://github.com/agentstacktech/vscode-plugin#readme)

Full MCP tool list: [MCP_SERVER_CAPABILITIES.md](../MCP_SERVER_CAPABILITIES.md).  
Data store (project.data, user.data): [DNA_KEY_VALUE_API.md](../architecture/DNA_KEY_VALUE_API.md).

---

## FAQ

| Question | Answer |
|----------|--------|
| **Full list of tools and parameters?** | [MCP_SERVER_CAPABILITIES.md](../MCP_SERVER_CAPABILITIES.md) |
| **Which tool for my task?** | [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) — domain map |
| **How to store/read project or user data?** | [DNA_KEY_VALUE_API.md](../architecture/DNA_KEY_VALUE_API.md) |
| **RAG collections and memory?** | [RAG_PLATFORM_GUIDE.md](../RAG_PLATFORM_GUIDE.md) · MCP `rag.*` actions |
