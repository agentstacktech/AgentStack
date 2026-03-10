# MCP и экосистема AgentStack — индекс

Единая точка входа в документацию по MCP (Model Context Protocol), плагинам, экосистеме API и примерам использования. Официальный репозиторий: [https://github.com/agentstacktech/AgentStack](https://github.com/agentstacktech/AgentStack).

---

## MCP (Model Context Protocol)

- **[MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md)** — полный список MCP tools (60+), параметры, примеры вызовов. Единый источник правды для плагинов Cursor, Claude, GPT, VS Code.
- **[api/mcp.md](api/mcp.md)** — конфигурация MCP, аутентификация, публичные эндпоинты, примеры для AI агентов.

---

## Plugins (плагины)

- **[plugins/README.md](plugins/README.md)** — индекс плагинов: Cursor, Claude Code, GPT (OpenAI), VS Code. Начало работы, получение API key, Quick Start по платформам. Код плагинов на GitHub: [cursor-plugin](https://github.com/agentstacktech/cursor-plugin), [claude-plugin](https://github.com/agentstacktech/claude-plugin), [gpt-plugin](https://github.com/agentstacktech/gpt-plugin), [vscode-plugin](https://github.com/agentstacktech/vscode-plugin).
- **[plugins/CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md)** — карта возможностей для AI: какой домен и какие tools использовать под запрос пользователя (Projects, 8DNA, Rules, Buffs, Payments, Auth и др.).

---

## Ecosystem API

- **[ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md)** — реализация Ecosystem API: data API для проектов, структура endpoints, примеры использования. Зачем и как использовать экосистемный API.
- **[architecture/DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md)** — хранилище данных (project.data, user.data), key-value API (GET/POST `/data`), работа с 8DNA.

---

## Примеры использования

- **[examples/mcp_complex_projects.md](examples/mcp_complex_projects.md)** — комплексные сценарии: SaaS с подписками, игра с монетизацией, маркетплейс с промо, образовательная платформа с триалами, enterprise с аналитикой. Синергия Buffs, Projects, Payments, Scheduler, Logic Engine.
- **[examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md)** — сценарии работы с баффами (триалы, продление, отмена).
- **[examples/mcp_buffs_temporary.md](examples/mcp_buffs_temporary.md)** — временные эффекты (триалы, промо).
- **[examples/mcp_buffs_persistent.md](examples/mcp_buffs_persistent.md)** — постоянные эффекты (подписки, разовые покупки).

---

**Быстрые ссылки для плагинов:** [MCP_SERVER_CAPABILITIES](MCP_SERVER_CAPABILITIES.md) · [Plugins index](plugins/README.md) · [CONTEXT_FOR_AI](plugins/CONTEXT_FOR_AI.md)
