# MCP chat command cookbook

Browser prompts for ChatGPT and Gemini using AgentStack MCP.  
**Genetic tag:** `core.mcp.universal_client.gen1`

## Before you start

1. Connect MCP or GPT Actions (see [MCP_CLIENT_COMPATIBILITY_MATRIX.md](./MCP_CLIENT_COMPATIBILITY_MATRIX.md)).
2. Discover unfamiliar actions: `list_actions` or `GET /mcp/actions`.
3. Confirm destructive / payment / access changes with the user.

## Projects

| Say (EN) | Say (RU) | Action |
|----------|----------|--------|
| List my projects | Покажи мои проекты | `projects.get_projects` |
| Create project without signup named X | Создай проект без регистрации с именем X | `projects.create_project_anonymous` |
| Stats for project 123 | Статистика проекта 123 | `projects.get_stats` |

**Confirmation (EN):** “This will create a new project and API keys. Proceed?”  
**Confirmation (RU):** «Будет создан новый проект и API-ключ. Продолжить?»

## Hosting

| Say (EN) | Action |
|----------|--------|
| Publish my site from ZIP | `hosting.deploy_files` (confirm) |
| What is my live site URL? | `hosting.demo.status` or project settings read |

## Payments

| Say (EN) | Action |
|----------|--------|
| Show wallet balance | `payments.get_balance` |
| Create payment for 10 USD | `payments.create` (**confirm amount**) |

## Agents

| Say (EN) | Action |
|----------|--------|
| List agents in this project | `agents.list` |
| Run agent X | `agents.run` (**confirm** if mutating) |

## Error recovery

| Code | User message |
|------|----------------|
| `auth_required` | Reconnect API key or OAuth in GPT/Gemini settings |
| `service_cap_denied` | Create a wider API key (`apikeys.create`) or use read-only discovery |
| `action_not_found` | Call `list_actions` — use exact names from catalog |

Recovery copy SoT: `provided_plugins/shared/plugin-kernel/canonicalCopy.mjs`

## Intent discovery (browser)

When unsure which action to call:

```http
POST https://agentstack.tech/mcp/discover/by_intent
Content-Type: application/json

{ "intent": "create a project and get an api key" }
```

Also exposed in `GET /mcp` → `discovery_meta.intent_url`.
