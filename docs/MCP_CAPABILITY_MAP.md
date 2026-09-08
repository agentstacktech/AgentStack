# MCP — возможности и метрики

> **Living SoT for agents:** [docs/plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md) (English, hot-path table, catalog hints).  
> **Action list:** `GET /mcp/actions` — counts and names are authoritative at runtime, not in this file.  
> **ADR:** [CAPABILITY_FIXTURE_TAXONOMY.md](adr/CAPABILITY_FIXTURE_TAXONOMY.md) · chronicle [MCP_SELF_DESCRIPTION_CHRONICLE.md](operations/MCP_SELF_DESCRIPTION_CHRONICLE.md).

## Полное управление (Full management)

При наличии соответствующих прав MCP даёт полный цикл управления по доменам:

- **Пользователи и участники:** projects.get_users, projects.add_user, projects.update_user_role, projects.remove_user
- **RBAC (роли и права):** rbac.get_roles, rbac.assign_role, rbac.revoke_role; для смены роли — projects.update_user_role. Права проверяются на сервере автоматически при вызовах; явная проверка rbac.check_permission — только когда нужно именно проверить право (редкий кейс).
- **Проекты:** get/create/update/delete, get_stats
- **Планировщик:** create_task, get_task, update_task, list_tasks, execute_task, cancel_task, delete_pool_task, clear_pool_tasks, refresh_pool
- **Ассеты:** assets.create, assets.get, assets.list, assets.update, assets.delete
- **Баффы:** create_buff, get_buff, list_active_buffs, get_effective_limits, apply_buff, extend_buff, revert_buff, cancel_buff, apply_temporary_effect, apply_persistent_effect
- **Логика:** logic.create, logic.update, logic.delete, logic.get, logic.list, logic.execute
- **Доступ к данным (FAP):** data_access.set_policy, get_policy, check_field, test_mask; плюс **глобальный шаблон:** get_defaults_template, set_defaults_template, apply_defaults_template — политики в 8DNA, кеш Neural Cache, инвалидация с RBAC. REST: [api/data-access-api.md](api/data-access-api.md).
- **Универсальные данные:** commands.execute (dna_crud по data_projects_8dna, data_projects_8dna, data_users_user и др.)
- **Валюты проекта:** настройка валют = ассеты с `type: "currency"` — assets.create, assets.get, assets.list, assets.update, assets.delete
- **Кошельки:** wallets.list, wallets.create, wallets.deposit, wallets.transfer; баланс — payments.get_balance

Права проверяются на стороне сервера (owner, manage_users, Professional для add/remove user, write для scheduler и т.д.).

**Инструкции и синергии:** см. [MCP_SYNERGIES_AND_INSTRUCTIONS.md](MCP_SYNERGIES_AND_INSTRUCTIONS.md) — пробелы по доменам, цепочки инструментов, рекомендуемый порядок операций, рецепты и workflows. **Полные флоу и steps:** [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) — все методы, ресурсы (list/subscribe/SSE/read), комбинации с таблицами шагов.

---

Описание текущего MCP: один инструмент **agentstack.execute**, батч шагов, discovery, actions, OAuth, AI, recipes, streaming, jobs. Base URL: `https://agentstack.tech/mcp`.

---

## Модель и протокол

| Критерий | MCP |
|----------|-----|
| **Модель инструментов** | Один инструмент `agentstack.execute`: батч шагов `steps[]`, один запрос — до 50 действий. Sync lane **mcp_batch = 60 с на весь батч**. Тяжёлые LLM (`bots.simulate`, `knowledge.playground`, `agents.run` wait=true): **не больше одного** на sync execute. Каталог: `GET /mcp/actions` → `execute_budget`. |
| **Протокол** | REST: `POST /mcp` с телом `{ "steps": [...] }`; совместимость JSON-RPC: `POST /mcp/tools` (tools/call). При tools/call с agentstack.execute в ответе возвращается результат **первого** шага; для полного набора шагов используйте batch `POST /mcp` |
| **Discovery** | `GET /mcp/discovery` и `GET /mcp/tools/list`, `POST /mcp` (method tools/list) — везде одна запись (agentstack.execute); список действий: `GET /mcp/actions` (live catalog) |
| **Выполнение** | Ссылки `{"from": "stepId.result.field"}` и `{"from": "context.field"}`, условия `if` |
| **Контекст** | В теле execute опционально `context: { "project_id", "user_id" }` — переопределяют сессию для всех шагов; при tools/call с agentstack.execute то же поле в `arguments.context`. Инструменты получают контекст через request.state |
| **Отчёт по баффам** | При успешных `buffs.apply_buff` / `buffs.apply_temporary_effect` в ответе поле `applied_buffs_summary`: кто какой бафф получил, applied_at, expires_at |
| **Стриминг** | `POST /mcp/stream` — SSE (started / completed) |
| **Асинхронные задачи** | `options.async: true` → `job_id`, статус: `GET /mcp/jobs/{job_id}` или MCP `discovery.job_status`. Durable lease ~20 мин; 180 с на **шаг**. Обязательно, если в батче больше одного тяжёлого LLM-шага. |
| **Health** | `GET /mcp/health` → `status`, `module`, `version`, `tools_count`, `discovery_url`, `actions_url` |
| **AI, recipes, OAuth** | `/mcp/ai_prompt*`, `/mcp/discover/*`, `/mcp/suggest/next`, `/mcp/recipes*`, `/mcp/patterns*`, `/mcp/examples*`, `POST /mcp/ai/plan_steps`, `.well-known` под `/mcp` |

---

## Метрики (замеры)

**Не храните latency/size в markdown** — каталог и счётчики меняются каждый релиз.

| Что нужно | Где |
|-----------|-----|
| Action count / domains | `GET /mcp/actions` или `docs/_generated/mcp_capability_coverage_snapshot.json` |
| Coverage % / sources | `GET /mcp/actions` + generated coverage snapshot in platform release |
| Latency / payload size (maintainer) | Platform CI metrics collection (maintainer runbook) |
| CI пороги | MCP integration tests in platform CI (see release notes) |

### Пороги из тестов (актуальны в CI)

| Метрика | Порог / значение | Где проверяется |
|---------|------------------|-----------------|
| Тесты | 99+ passed | `pytest mcp/tests tests/test_mcp_gpt_scan_flow.py` |
| Размер tools/list | < 100 KB | test_mcp_metrics, test_mcp_gpt_scan_flow |
| Латентность discovery / tools/list | p95 < 2000 ms | test_mcp_performance |
| Валидация: пустые steps | 400 | test_mcp_v2_executor (TestMcpValidation), test_mcp_v2_contract (TestMcpExecuteContract) |
| Валидация: дубликаты step.id | 400, "Duplicate step id" | test_mcp_v2_executor (TestMcpValidation) |
| Лимит шагов | 50, 400 при превышении | test_mcp_v2_executor (TestMcpValidation) |
| Контракт discovery | protocol_version, capabilities, tools[0].name=agentstack.execute, actions_url | test_mcp_discovery |
| Совместимость | Cursor, Claude, GPT scan flow | test_cursor_compatibility, test_claude_compatibility, test_mcp_gpt_scan_flow |

Запуск: из `agentstack-core`: `python -m pytest mcp/tests tests/test_mcp_gpt_scan_flow.py -v` или `python mcp/run_all_tests.py`.

