# Plugins: Claude, Cursor, GPT, VS Code — Отличия и решения

**Version:** 0.3  
**Date:** 2026-02-23  
**Context:** Плагины AgentStack для **Claude Code**, **Cursor**, **GPT (OpenAI)** и **VS Code**; согласование и философия. Индекс плагинов: [docs/plugins/README.md](README.md).

---

## Размещение

Все плагины в каталоге **provided_plugins/** (см. [provided_plugins/README.md](../../provided_plugins/README.md)):

- **Cursor:** `provided_plugins/cursor-plugin/`
- **Claude:** `provided_plugins/claude-plugin/`
- **GPT (OpenAI):** `provided_plugins/gpt-plugin/`
- **VS Code:** `provided_plugins/vscode-plugin/`

Один плагин — один артефакт (Decomposition). Общий MCP endpoint и экосистема; манифесты и конфиги различаются по платформе.

---

## GPT (OpenAI) — GPT Actions

- **Модель интеграции:** не «пакет плагина», а артефакты для **GPT Actions**. Пользователь создаёт **Custom GPT** в ChatGPT и подключает OpenAPI 3.1 схему + инструкции.
- **Манифест:** OpenAPI 3.1 схема (`provided_plugins/gpt-plugin/openapi/agentstack-mcp.yaml`) + эталонные инструкции (`GPT_INSTRUCTIONS.md`). Установка = создание Custom GPT по `GPT_QUICKSTART.md`.
- **MCP:** тот же endpoint `https://agentstack.tech/mcp`; аутентификация — API Key в заголовке `X-API-Key` (настраивается в Custom GPT → Action → Authentication).
- **OAuth (опционально):** для Custom GPT можно использовать OAuth2 (Authorization Code) с AgentStack как IdP:
  - Authorization URL: `https://agentstack.tech/api/oauth2/authorize`
  - Token URL: `https://agentstack.tech/api/oauth2/token`
- **Подробнее:** структура артефактов в `provided_plugins/gpt-plugin/ARTIFACTS.md`; быстрый старт в `provided_plugins/gpt-plugin/GPT_QUICKSTART.md`.

---

## Отличия по компонентам

| Компонент | Cursor | Claude Code | GPT (OpenAI) | VS Code |
|-----------|--------|-------------|--------------|---------|
| **Манифест** | `.cursor-plugin/plugin.json` | `.claude-plugin/plugin.json` | OpenAPI 3.1 schema + GPT_INSTRUCTIONS.md | `package.json` + `contributes.mcpServerDefinitionProviders` |
| **Установка** | Копирование плагина + MCP config | Установка плагина + `claude mcp add` | Создание Custom GPT, вставка схемы и инструкций | Marketplace/VSIX + один раз ввод API key |
| **MCP config** | `mcp.json` (HTTP: `type`, `baseUrl`, `headers`) | HTTP через пользовательскую настройку (см. ниже) | API Key или OAuth в настройках Action | HTTP через расширение (авто-регистрация) |
| **Skills** | `skills/*/SKILL.md` (8DNA, Projects, Rules Engine, Assets, RBAC, Buffs, Payments, Auth) | тот же формат | Нет аналога; контекст в инструкциях Custom GPT | Нет аналога; контекст в MCP и README |
| **Rules** | `rules/*.mdc` (Cursor-specific) | Нет аналога; знания в Skills + ссылки на доки | Нет аналога | Нет аналога |

---

## MCP: HTTP в Claude Code

- В **плагине** Claude Code (`.mcp.json`) в официальной документации описан только запуск MCP через **command** (stdio). Для **remote HTTP** серверов пользователь настраивает MCP вручную.
- **Рекомендуемый сценарий для AgentStack:** пользователь после установки плагина выполняет один раз:
  ```bash
  claude mcp add --transport http agentstack https://agentstack.tech/mcp --header "X-API-Key: <YOUR_API_KEY>"
  ```
  Либо настраивает MCP в UI Claude Code (если доступно) по инструкции в MCP_QUICKSTART.md.

- **Итог:** в `provided_plugins/claude-plugin/` не добавляем `.mcp.json` с HTTP (формат для HTTP в plugin bundle не зафиксирован); все шаги подключения MCP описываем в `MCP_QUICKSTART.md` и README (Elegant Minimalism: минимум шагов, один API key).

### Claude и OAuth

- Текущий рекомендованный путь для Claude Code — API key (`X-API-Key`) для MCP HTTP.
- Если платформа Claude/каталог MCP требует OAuth, AgentStack уже предоставляет стандартные OAuth2 endpoints (`/api/oauth2/authorize`, `/api/oauth2/token`) без отдельной реализации «под Claude».

---

## Что переиспользуется

- Тексты и структура **Skills** (8DNA, Projects, Rules Engine, **Assets**, **RBAC**, **Buffs**, **Payments**, **Auth**) — копируем и при необходимости адаптируем frontmatter; заменяем «Cursor» на «Claude Code» в инструкциях.
- **GPT:** те же MCP endpoint и API key; описание получения ключа переиспользуется из MCP_QUICKSTART; инструкции Custom GPT ссылаются на MCP_SERVER_CAPABILITIES.
- Production URL MCP: `https://agentstack.tech/mcp`.
- Документация: ссылки на MCP_SERVER_CAPABILITIES, 8DNA, ecosystem без дублирования.

---

## Skills: синхронизация Cursor и Claude

- **Источник правды:** правки вносятся в `provided_plugins/cursor-plugin/skills/`. При релизе или обновлении skills копировать содержимое в `provided_plugins/claude-plugin/skills/` (каталоги: agentstack-8dna, agentstack-projects, agentstack-rules-engine, agentstack-assets, agentstack-rbac, agentstack-buffs, agentstack-payments, agentstack-auth).
- **Адаптация для Claude:** в скопированных SKILL.md заменить в тексте «Cursor» на «Claude Code» (например: «add MCP in Cursor» → «add MCP in Claude Code»). Frontmatter (name, description) не менять.
- **Ссылки в Claude-варианте:** References на MCP_QUICKSTART и README указывают на артефакты в корне claude-plugin (MCP_QUICKSTART.md, README.md — в том же плагине). Ссылки на репо (MCP_SERVER_CAPABILITIES, philosophy) остаются общими.
- **Версионирование:** при изменении skills обновлять CHANGELOG в обоих плагинах (Time Processes Philosophy). См. также [SKILLS_AUTHORING_GUIDE.md](SKILLS_AUTHORING_GUIDE.md).

---

**Genetic code (reference):** `docs.plugins.claude_vs_cursor_plugin.gen2`
