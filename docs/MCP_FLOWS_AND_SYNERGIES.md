# MCP: полный набор флоу и синергии

Справочник по всем возможным флоу MCP, комбинациям вызовов и пошаговым сценариям с полным набором steps.

---

## 1. Обзор транспортов и методов

| Транспорт | Эндпоинт | Назначение |
|-----------|----------|------------|
| **JSON-RPC 2.0** | `POST /mcp` (тело: `method`, `params`, `id`) | Единая точка: initialize, tools/list, tools/call, prompts/*, resources/* |
| **REST** | `GET /mcp/discovery` | Discovery: один инструмент agentstack.execute, actions_url |
| **REST** | `GET /mcp/actions` | Список всех action (по доменам) для steps |
| **REST** | `GET /mcp/tools/list` | То же что tools/list — один tool + actions_url |
| **REST** | `POST /mcp` | Батч execute (steps[], context, options) — полный результат по всем шагам |
| **REST** | `POST /mcp/stream` | Стриминг выполнения (SSE) |
| **REST** | `GET /mcp/jobs/{job_id}` | Статус асинхронного job (при options.async: true) |
| **SSE** | `GET /mcp/resources/notifications` | Подписка на уведомления об изменении ресурсов (connection_id) |
| **REST** | `GET /mcp/health` | Healthcheck + pointers to discovery/actions (`discovery_url`, `actions_url`) |

---

## 2. Все JSON-RPC методы (POST /mcp)

| method | Описание | Параметры | Ответ |
|--------|----------|-----------|--------|
| `initialize` | Handshake MCP | (опционально) | protocolVersion, capabilities, serverInfo |
| `ping` | Проверка живости | — | pong |
| `tools/list` | Список инструментов | — | tools: [agentstack.execute], actions_url |
| `tools/call` | Вызов инструмента | name, arguments (или params) | result.content (текст) или isError |
| `prompts/list` | Список промптов | — | prompts[] (кэш ~60 сек) |
| `prompts/get` | Один промпт | name | prompt |
| `resources/list` | Список ресурсов пользователя | — | resources[] (uri, name, description, mimeType) |
| `resources/subscribe` | Подписка на ресурс | uri, connection_id? | subscribed, uri, connection_id |
| `resources/unsubscribe` | Отписка | uri, connection_id? | unsubscribed, uri, connection_id |
| `resources/read` | Чтение ресурса по URI | uri | result.contents[] (uri, mimeType, text) |
| `notifications/*` | Уведомление (без id) | — | 200 + acknowledged (для совместимости сканеров) |

**Важно:** при `tools/call` с `name: "agentstack.execute"` в `arguments` передаётся `steps` (массив); в ответе возвращается результат **первого** шага. Для полного набора шагов используйте `POST /mcp` с телом `{ "steps": [...] }`.

---

## 3. Флоу: последовательность при первом подключении (полный набор steps)

### 3.1 Инициализация и discovery

```
Step 1.  POST /mcp  →  { "jsonrpc": "2.0", "method": "initialize", "id": "1" }
        Ответ: protocolVersion, capabilities (tools, resources, prompts), serverInfo

Step 2.  GET /mcp/actions  (или оставить на потом)
        Ответ: domains (projects, buffs, auth, ...), total_actions, entrypoint: agentstack.execute

Step 3.  POST /mcp  →  { "method": "tools/list", "id": "2" }
        Ответ: tools: [{ name: "agentstack.execute", inputSchema, ... }], actions_url: "/mcp/actions"
```

После этого клиент знает: один инструмент `agentstack.execute`, список action — в GET /mcp/actions.

### 3.2 Выполнение: батч (полный набор шагов)

```
Step 4.  POST /mcp  (REST, не JSON-RPC)  →  Body:
        {
          "context": { "project_id": 123, "user_id": 456 },   // опционально
          "steps": [
            { "id": "s1", "action": "projects.get_project", "params": { "project_id": 123 } },
            { "id": "s2", "action": "projects.get_users", "params": { "project_id": 123 } },
            { "id": "s3", "action": "rbac.get_roles", "params": { "project_id": 123 } }
          ],
          "options": { "stopOnError": true }
        }
        Ответ: { "steps": [ { "id": "s1", "result": {...} }, ... ], "context": {...} }
```

Или через JSON-RPC tools/call (тогда только первый шаг в ответе):

```
Step 4'.  POST /mcp  →  { "method": "tools/call", "params": { "name": "agentstack.execute", "arguments": { "steps": [ ... ] } }, "id": "4" }
        Ответ: result.content[0].text = JSON результата первого шага
```

### 3.3 Выполнение: один именованный инструмент (из registry)

```
Step 4''.  POST /mcp  →  { "method": "tools/call", "params": {
        "name": "projects.get_projects",   // или projects_get_projects
        "arguments": { "project_id": 123 }
      }, "id": "4" }
        Ответ: result.content[0].text = JSON ответа инструмента
```

Имена из MCP_TOOLS_REGISTRY: домен.действие (например rbac.get_roles, rbac.assign_role, buffs.apply_buff).

---

## 4. Флоу: ресурсы (list → subscribe → SSE → read)

Полный набор steps для работы с ресурсами и уведомлениями.

### 4.1 Список ресурсов и чтение без подписки

```
Step 1.  POST /mcp  →  { "method": "resources/list", "id": "r1" }
        Заголовок: X-API-Key (или сессия).
        Ответ: result.resources[] — agentstack://me (если включено), agentstack://projects/{id} для проектов пользователя.

Step 2.  POST /mcp  →  { "method": "resources/read", "params": { "uri": "agentstack://me" }, "id": "r2" }
        Ответ: result.contents[0].text — JSON профиля текущего пользователя (ecosystem).

Step 3.  POST /mcp  →  { "method": "resources/read", "params": { "uri": "agentstack://projects/123" }, "id": "r3" }
        Ответ: result.contents[0].text — JSON проекта 123.

Step 4.  POST /mcp  →  { "method": "resources/read", "params": { "uri": "agentstack://projects/123/users/456" }, "id": "r4" }
        Ответ: result.contents[0].text — JSON участника 456 в проекте 123 (при наличии прав).
```

Допустимые URI: `agentstack://me`, `agentstack://projects/{project_id}`, `agentstack://projects/{project_id}/users/{user_id}`.

### 4.2 Подписка на изменения и SSE (полный цикл)

```
Step 1.  GET /mcp/resources/notifications  (Accept: text/event-stream, заголовок X-API-Key)
        Ответ: SSE stream; первое событие "connected" с connection_id в data.

Step 2.  Сохранить connection_id из события connected.

Step 3.  POST /mcp  →  { "method": "resources/subscribe", "params": {
        "uri": "agentstack://projects/123",
        "connection_id": "<connection_id из Step 1>"
      }, "id": "sub1" }
        Ответ: result.subscribed: true, uri, connection_id.

Step 4.  При изменении проекта 123 (например через projects.update_project) сервер шлёт по SSE:
        event: notification
        data: {"jsonrpc":"2.0","method":"notifications/resources/updated","params":{"uri":"agentstack://projects/123"}}

Step 5.  Клиент при получении notification вызывает resources/read для обновления данных:
        POST /mcp  →  { "method": "resources/read", "params": { "uri": "agentstack://projects/123" }, "id": "r5" }

Step 6.  Отписка:  POST /mcp  →  { "method": "resources/unsubscribe", "params": { "uri": "agentstack://projects/123", "connection_id": "..." }, "id": "unsub1" }
```

Поддерживаемые URI для subscribe: `agentstack://me`, `agentstack://projects/{project_id}`. Без `connection_id` создаётся session-based подписка (уведомления не придут, пока не откроется SSE с последующей миграцией на connection_id).

---

## 5. Флоу: промпты + инструменты

```
Step 1.  POST /mcp  →  { "method": "prompts/list", "id": "p1" }
        Ответ: result.prompts[] (name, description, arguments).

Step 2.  POST /mcp  →  { "method": "prompts/get", "params": { "name": "create_logic_rule" }, "id": "p2" }
        Ответ: result.prompt (template, arguments) — пошаговая инструкция.

Step 3.  Выполнить шаги из промпта через agentstack.execute (POST /mcp с steps или tools/call):
        например logic.get_processors → logic.get_commands → logic.create с нужными params.
```

---

## 6. Синергии: комбинации инструментов с полным набором steps

### 6.1 Проект + экономика (валюты, кошельки)

| Step | Action / вызов | Параметры (пример) | Результат |
|------|----------------|--------------------|-----------|
| 1 | projects.create_project_anonymous | name, description | project_id, user_id, user_api_key в context |
| 2 | assets.create | name: "GOLD", type: "currency", price_usdt: "0", components | asset_id |
| 3 | assets.create | name: "GEM", type: "currency", ... | asset_id |
| 4 | wallets.create | project_id (from s1), user_id (from s1), name, currency | wallet_id |
| 5 | wallets.deposit | project_id, user_id, wallet_id (from s4), amount, currency | — |
| 6 | logic.create | (опционально) правила начисления | — |

Контекст: задать `context: { "project_id": {"from": "s1.result.project_id"}, "user_id": {"from": "s1.result.user_id"} }` или передать после шага 1.

### 6.2 Проект + участники + RBAC

| Step | Action | Параметры | Результат |
|------|--------|-----------|-----------|
| 1 | projects.get_project | project_id | project |
| 2 | projects.get_users | project_id | users[] |
| 3 | rbac.get_roles | project_id | roles[] |
| 4 | rbac.assign_role | project_id, user_id, role_id | — |
| 5 | rbac.revoke_role | project_id, user_id, role_id | — |
| 6 | rbac.check_permission | project_id, permission, user_id? | granted |
| 7 | projects.update_user_role | project_id, user_id, role | (альтернатива смены роли через проект) |

Права (manage_users, admin и т.д.) проверяются на сервере; явная проверка — через rbac.check_permission при необходимости.

### 6.3 Триал и подписка (SaaS)

| Step | Action | Параметры | Результат |
|------|--------|-----------|-----------|
| 1 | projects.create_project_anonymous | name, description | project_id, user_id |
| 2 | buffs.create_buff | name, duration_days, effects, revert_on_expire | buff_id |
| 3 | logic.create | triggers: user.created, space: вызов apply_buff | logic_id |
| 4 | buffs.apply_buff | buff_id (from s2), entity_kind: user, entity_id | — |
| 5 | buffs.create_buff | (шаблон подписки) | buff_id_premium |
| 6 | payments.create | (при оплате) | payment_id |
| 7 | buffs.revert_buff | (триал) + buffs.apply_buff (premium) | — |
| 8 | scheduler.create_task | cron для проверки истекших триалов | task_id |

### 6.4 Ресурсы + инструменты (актуальные данные после изменений)

| Step | Действие | Описание |
|------|----------|----------|
| 1 | GET /mcp/resources/notifications | Открыть SSE, получить connection_id |
| 2 | resources/subscribe | uri: agentstack://projects/123, connection_id | Подписка на проект |
| 3 | tools/call или POST /mcp steps | projects.update_project, project_id: 123, ... | Изменить проект |
| 4 | (сервер) | notify_resource_updated(uri) → SSE notification | Клиент получает event |
| 5 | resources/read | uri: agentstack://projects/123 | Получить обновлённый JSON проекта |

### 6.5 Планировщик + логика + баффы

| Step | Action | Параметры | Результат |
|------|--------|-----------|-----------|
| 1 | scheduler.create_task | cron, task_data (например command для logic.execute) | task_id |
| 2 | logic.create | triggers по command, space с processors | logic_id |
| 3 | scheduler.execute_task | task_id (или ждать cron) | — |
| 4 | buffs.apply_temporary_effect | duration_days, on_expire: действия | — |

### 6.6 Универсальные данные (commands) + логика

| Step | Action | Параметры | Результат |
|------|--------|-----------|-----------|
| 1 | commands.execute | command_type: dna_crud, target_entity, operation_type: get/list/create/update/delete, payload | data |
| 2 | logic.get_processors | — | processors[] |
| 3 | logic.get_commands | — | commands[] |
| 4 | logic.create | triggers, space (processors) | logic_id |
| 5 | logic.execute | logic_id, payload | — |

### 6.7 Аналитика + лимиты + баффы (авто-масштабирование)

| Step | Action | Параметры | Результат |
|------|--------|-----------|-----------|
| 1 | analytics.get_usage | project_id, (период) | usage |
| 2 | buffs.get_effective_limits | project_id, user_id? | limits |
| 3 | logic.create | триггер по метрике/порогу | logic_id |
| 4 | buffs.apply_temporary_effect | при достижении порога — временное повышение лимитов | — |

---

## 7. Готовые цепочки (TOOL_CHAINS из tool_graph)

| Цепочка | Инструменты по порядку | Категория |
|---------|------------------------|-----------|
| user_with_trial | projects.create_project_anonymous → buffs.create_buff → buffs.apply_buff | saas |
| scheduled_rewards | logic.create → scheduler.create_task → buffs.apply_temporary_effect | game |
| payment_to_subscription | payments.create → buffs.create_buff → buffs.apply_buff → scheduler.create_task | saas |
| trial_to_premium | payments.create → buffs.revert_buff → buffs.apply_buff | saas |
| game_setup | projects.create_project_anonymous → commands.execute → logic.create (x3) | game |
| crud_with_logic | commands.execute → logic.create → logic.execute | backend_api |
| usage_monitoring | analytics.get_usage → logic.create → buffs.apply_temporary_effect | saas |
| apply_custom_buff_to_user | projects.get_projects/get_project → projects.get_users → buffs.list_active_buffs → buffs.create_buff → buffs.apply_buff | buffs |
| rbac_manage_members | projects.get_project → projects.get_users → rbac.get_roles → rbac.assign_role / rbac.revoke_role / rbac.check_permission | rbac |

Рецепты (пошаговая валидация): `GET /mcp/recipes`, `GET /mcp/recipes/{id}`, `POST /mcp/recipes/{id}/validate`. Доступные id: card_game_basic, saas_trial_system, apply_custom_buff_to_user, project_economy.

---

## 8. Зависимости инструментов (кратко)

- **Перед вызовом:** buffs.apply_buff требует buffs.create_buff; logic.execute требует logic.create; scheduler.* требуют project context; commands.execute — project context.
- **Часто вместе:** projects.get_project + projects.get_users + rbac.get_roles; buffs.apply_buff + payments.create; logic.create + scheduler.create_task.
- **Конфликты:** buffs.extend_buff vs buffs.revert_buff; auth.register vs projects.create_project_anonymous (разные пути пользователя).

Полный граф: `mcp/tool_graph.py` — TOOL_DEPENDENCIES, get_required_tools, get_related_tools, suggest_next_tools, validate_tool_sequence.

---

## 9. Чеклист для агента (полный набор steps)

1. **Нужен проект** → steps: projects.create_project_anonymous (или create_project); сохранить project_id, user_api_key.
2. **Нужны валюты** → steps: assets.create (type: currency) для каждой валюты; assets.list для списка.
3. **Кошельки** → wallets.create → wallets.deposit / wallets.transfer; баланс — payments.get_balance.
4. **Триалы/подписки** → buffs.create_buff + logic.create (триггер) + buffs.apply_buff / apply_temporary_effect.
5. **Периодические задачи** → scheduler.create_task (cron); обновление — scheduler.update_task; отмена — scheduler.cancel_task.
6. **Участники и роли** → projects.get_users; rbac.get_roles; rbac.assign_role / revoke_role; projects.update_user_role при правах.
7. **Проверка прав** → rbac.check_permission(project_id, permission, user_id?).
8. **Ресурсы** → resources/list → resources/read(uri); для обновлений в реальном времени: GET /mcp/resources/notifications → resources/subscribe → при notification → resources/read.
9. **Промпты** → prompts/list → prompts/get(name) → выполнить шаги из template через agentstack.execute.
10. **Универсальные данные** → commands.execute (dna_crud) по target_entity; commands.execute_batch для пачки.

---

См. также: [MCP_SYNERGIES_AND_INSTRUCTIONS.md](MCP_SYNERGIES_AND_INSTRUCTIONS.md), [MCP_CAPABILITY_MAP.md](MCP_CAPABILITY_MAP.md), `mcp/tool_graph.py`, `mcp/workflows.py`, `mcp/ai_prompt.py`.
