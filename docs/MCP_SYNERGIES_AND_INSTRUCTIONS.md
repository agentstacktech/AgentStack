# MCP: синергии, пробелы и инструкции

> **Living SoT для агентов (EN):** [plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md) — hot-path table, catalog hints.  
> **Каталог действий:** `GET /mcp/actions` — имена и счётчики только из live API.  
> **ADR:** [adr/CAPABILITY_FIXTURE_TAXONOMY.md](adr/CAPABILITY_FIXTURE_TAXONOMY.md) · [operations/MCP_SELF_DESCRIPTION_CHRONICLE.md](operations/MCP_SELF_DESCRIPTION_CHRONICLE.md).

Единый справочник по возможностям MCP, сочетанию инструментов и рекомендуемым сценариям.

**Полный набор флоу и steps:** см. [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) — все JSON-RPC методы, ресурсы (list → subscribe → SSE → read), комбинации инструментов с пошаговыми таблицами и цепочки (TOOL_CHAINS).

**Бизнес-флоу и синергии по доменам:** [MCP_BUSINESS_FLOWS.md](MCP_BUSINESS_FLOWS.md) — 12 реальных сценариев с JSON-примерами.

**Плейбуки по настройке проекта:** [MCP_PROJECT_SETUP_PLAYBOOKS.md](MCP_PROJECT_SETUP_PLAYBOOKS.md) — 11 плейбуков, включая массовую конфигурацию.

---

## 1. Что покрыто, чего не хватает

### Полностью покрыто (CRUD / полное управление при наличии прав)


| Домен                     | Инструменты                                                                                                                                                              | Примечание                                                                        |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| **Проекты**               | projects.get_projects, get_project, get_stats, get_users, create_project, create_project_anonymous, update_project, delete_project                                       | —                                                                                 |
| **Участники**             | projects.get_users, add_user, update_user_role, remove_user                                                                                                              | add/remove — Professional или экосистема                                          |
| **Планировщик**           | scheduler.create_task, get_task, update_task, list_tasks, execute_task, cancel_task, delete_pool_task, clear_pool_tasks, refresh_pool, get_all_db_tasks                  | —                                                                                 |
| **Ассеты**                | assets.create, get, list, update, delete                                                                                                                                 | В т.ч. валюты: type=currency                                                      |
| **Баффы**                 | buffs.create_buff, get_buff, list_active_buffs, get_effective_limits, apply_buff, extend_buff, revert_buff, cancel_buff, apply_temporary_effect, apply_persistent_effect | —                                                                                 |
| **Логика**                | logic.create, update, delete, get, list, execute, get_processors, get_commands, flush_batch                                                                              | —                                                                                 |
| **Кошельки**              | wallets.list, wallets.create, wallets.deposit, wallets.transfer                                                                                                          | + payments.get_balance                                                            |
| **Платежи**               | payments.create, get, list_transactions, get_balance, refund                                                                                                             | —                                                                                 |
| **API-ключи**             | apikeys.create, list, delete                                                                                                                                             | + service_caps (L1 gate) — see `GET /mcp/actions` domain `apikeys` |
| **Данные (универсально)** | commands.execute, commands.execute_batch                                                                                                                                 | dna_crud по сущностям                                                             |
| **Аутентификация**        | auth.login, register, get_profile, update_profile                                                                                                                        | —                                                                                 |
| **Аналитика**             | analytics.get_usage, get_metrics                                                                                                                                         | чтение метрик                                                                     |
| **Процессоры**            | processors.list, get_metadata, execute                                                                                                                                   | для логики                                                                        |
| **RAG / база знаний**     | rag.collection_create/list/delete, rag.document_add/list/delete, rag.search, rag.memory_add/get/search                                                                   | семантический поиск и память                                                      |
| **Доступ к полям (FAP)**  | data_access.get_policy/set_policy, get_triggers/set_triggers, check_field, test_mask, get_defaults_template/set_defaults_template/apply_defaults_template                | L2 маски и политики полей                                                         |
| **Социальные**            | social.channels.*, social.chat.*, social.friends.*, social.followers.*, social.privacy.*, social.public.*, social.users.*, social.federation.*, social.merge_feed        | полный набор социальных инструментов                                              |
| **RBAC**                  | rbac.get_roles, assign_role, revoke_role, check_permission                                                                                                               | управление ролями и правами                                                       |


### Частично или не в MCP


| Область                | Статус                                                 | Как обойти                                            |
| ---------------------- | ------------------------------------------------------ | ----------------------------------------------------- |
| **Webhooks**           | Нет отдельного домена `webhooks.*`                     | `integrations.install_recipe`, `integrations.list_connections`, `logic.create` |
| **Уведомления (push)** | Каноническое имя `notifications.send_push`             | `notifications.send` — deprecated shim (ошибка в каталоге); не `send_notification` |
| **Экспорт аналитики**  | analytics.export_data в старых примерах (без MCP action) | `analytics.get_metrics`, `analytics.get_usage`                    |


### Commerce: маркетплейс, аукционы, межпроектный обмен


| Задача                                        | Канал                                                             | Примечание                                                                                                                   |
| --------------------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Листинг, ставки, аукцион, принятие сделки     | **REST** `/api/marketplace/...`                                   | Нет отдельных `marketplace.*` в `agentstack.execute`. Подсказки путей: `**GET /mcp/actions`** → домен `**commerce_rest**`.   |
| Котировка и исполнение обмена между проектами | **REST** `POST /api/exchange/quote`, `POST /api/exchange/execute` | Не то же самое, что protein `commands.execute` с `command_type: exchange`.                                                   |
| Платежи, баланс, кошельки, ассеты             | **MCP** `payments.*`, `wallets.*`, `assets.*`                     | Канонические имена: `payments.create`, `payments.get`, `payments.get_balance` (не legacy `create_payment` / wallets.get_balance). |


**Типичная цепочка (маркетплейс + фиат):** `assets.create` (если нужен торгуемый актив) → HTTP создание листинга → ставки/accept по REST → при необходимости MCP `payments.create` для отдельного списания.

**Карта и промпт для ИИ:** [MCP_OVERVIEW.md](MCP_OVERVIEW.md) · MCP prompt `**agentstack_commerce_flows`** (`prompts/list` / `prompts/get`).

---

## 2. Синергии (как сочетать инструменты)

### Цепочки по смыслу

- **Проект → экономика**  
`projects.create_project_anonymous` → `assets.create` (type: currency) → `wallets.create` → `wallets.deposit`  
Сначала проект (credentials на верхнем уровне + neutral `bootstrap` — настроить заголовки MCP-клиента один раз), потом валюты как ассеты, затем кошельки и пополнение.
- **Проект → участники**  
`projects.get_users` → `projects.add_user` / `projects.update_user_role` / `projects.remove_user`  
Управление составом при наличии прав (owner / manage_users, Professional для add/remove).
- **Триал/подписка**  
`buffs.create_buff` (шаблон триала) → `logic.create` (триггер user.created) → `buffs.apply_buff` / `buffs.apply_temporary_effect`  
Авто-назначение триала новым пользователям.
- **Платёж → эффект**  
`payments.create` → `buffs.apply_buff` или `buffs.apply_persistent_effect`  
После успешной оплаты — выдача подписки или постоянного эффекта.
- **Расписание + логика**  
`scheduler.create_task` (cron) → в task_data вызов `logic.execute` или команды  
Периодические проверки (напоминания, отчёты, напоминания об истечении триала).
- **Лимиты и авто-масштабирование**  
`logic.create` (триггер по метрике) + `analytics.get_usage` + `buffs.apply_temporary_effect`  
При достижении порога — временное повышение лимитов через бафф.
- **Глобальное событие**  
`buffs.apply_buff` / `buffs.apply_temporary_effect` с entity_kind=project, entity_id=project_id  
Один бафф на проект — действует на всех пользователей.
- **Универсальные данные**  
`commands.execute` (dna_crud: get/create/update/delete) по data_projects_8dna, data_projects_8dna, data_users_user и др.  
Любые данные при наличии прав; валюты и конфиг проекта — через ассеты и project.data.
- **RAG → поиск → действие**  
`rag.collection_create` → `rag.document_add` (индексировать контент) → `rag.search` (семантический поиск) → использовать результат в `logic.execute` или `commands.execute`.  
Типичный сценарий: база знаний продукта, FAQ-бот, AI-контекст для агента.
- **RAG memory (кратко/долгосрочная память агента)**  
`rag.memory_add` (запомнить факт/пользователя) → `rag.memory_search` (вспомнить в следующем запросе) → `rag.memory_get` (прямое чтение по id).  
Используется для персонализированных ИИ-ассистентов без внешней векторной БД.
- **FAP (доступ к полям) — настройка масок и политик**  
`data_access.get_policy` (читать текущую политику) → `data_access.set_policy` (установить маски/правила) → `data_access.test_mask` (проверить маску на тестовых данных) → `data_access.apply_defaults_template` (применить шаблон по умолчанию).  
Для настройки видимости данных пользователей; нужен до начала работы с user-данными в продакшне.
- **Социальный граф → действие**  
`social.users.lookup` (найти пользователей) → `social.friends.request` (добавить в друзья) → `social.channels.register` (создать канал) → `social.chat.post` (отправить сообщение) → `social.public.publish` (публикация профиля).
- **API ключ с ограниченными правами (безопасный агент)**  
`apikeys.create` с `service_caps: ["payments", "scheduler"]` → передать ключ ИИ-агенту → агент не может удалить проект или изменить RBAC.  
Подробнее: MCP prompt `agentstack_api_key_safety` and `apikeys.*` actions in [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md).

### Парадигмы (из AI prompt)

- **Баффы = таймеры с хуками**: on_apply, on_expire, revert_on_expire; не только подписки, но и отложенные действия, кулдауны, авто-очистка.
- **Пользователи = контейнеры**: data_users_user для NPC, сессий, ботов, джобов; идентификация по email/user_id.
- **Проект = глобальное состояние**: project.data виден всем; бафф на проект меняет правила для всех.
- **RAG = долгосрочная память экосистемы**: коллекции — это изолированные векторные индексы; memory — персонализированное хранилище на уровне пользователя.
- **FAP = L2 фильтрация данных**: data_access настраивает что и как видят разные роли; работает поверх RBAC (L1 = service_caps, L2 = FAP маски).
- **Social = граф взаимодействий**: channels для группового общения; friends/followers для связей; public/privacy для видимости; merge_feed для агрегации.

---

## 3. Рекомендуемый порядок операций

### Новый проект с экономикой

1. `projects.create_project_anonymous` (или `projects.create`) — использовать `project_id`; для anonymous: `user_api_key` / `session_token` + neutral `bootstrap` (настроить заголовки клиента, не память модели). **Cursor plugin:** `/agentstack-authorize` вместо anonymous.
2. `assets.create` с type=currency для каждой валюты (GOLD, GEM и т.д.).
3. `wallets.create` (project_id, при необходимости user_id для экосистемы).
4. `wallets.deposit` для начального пополнения.
5. При необходимости: `logic.create` для правил начисления/списания; `buffs.create_buff` для бонусов/событий.

### SaaS: триал и подписка

1. `projects.create_project_anonymous`.
2. `buffs.create_buff` — шаблон триала (duration_days, effects с limits).
3. `logic.create` — триггер user.created, в space вызов применения баффа (или явно buffs.apply_buff после регистрации).
4. `buffs.create_buff` — шаблон подписки (persistent, extends_on_reapply).
5. `payments.create` при оплате → затем `buffs.revert_buff` (триал) + `buffs.apply_buff` (подписка).
6. `scheduler.create_task` для проверки истекших триалов и напоминаний.

### Игра: валюта и награды

1. Проект + ассеты-валюты (assets.create type=currency).
2. Кошельки (wallets.create) или учёт в user.data через commands.execute.
3. `buffs.apply_persistent_effect` или `buffs.apply_buff` для наград (валюта, предметы в data).
4. `scheduler.create_task` для ежедневных наград; `logic.create` для правил квестов/условий.

### RAG / база знаний

1. `rag.collection_create` — создать коллекцию (name, description, embedding_model — опционально).
2. `rag.document_add` — загрузить документы (content, metadata: { source, category, ... }).
3. `rag.search` — семантический поиск (query, collection_id, limit, min_score).
4. Опционально: `rag.memory_add` для пользовательских воспоминаний; `rag.memory_search` для контекста разговора.
5. При необходимости: `rag.document_list` (список doc_id), `rag.document_delete` (удалить устаревший контент).

### FAP (Field Access Policy) — настройка прав на поля

1. `data_access.get_policy` — прочитать текущую политику проекта.
2. `data_access.get_defaults_template` — получить шаблон по умолчанию для роли.
3. `data_access.set_policy` — задать политику маскирования полей (по роли / user_id).
4. `data_access.set_triggers` — задать триггеры (хуки до/после чтения, записи).
5. `data_access.test_mask` — проверить маску на тестовых данных перед применением в продакшне.
6. `data_access.apply_defaults_template` — применить готовый шаблон ко всем пользователям роли.

### Социальный профиль пользователя

1. `social.public.publish` — создать публичный профиль (username, bio, avatar_url).
2. `social.privacy.put` — задать настройки приватности (who_can_send_friend_request, who_can_see_friends и т.д.).
3. `social.channels.register` — создать канал (name, type: group/dm, members).
4. `social.friends.request` — отправить заявку в друзья (target_user_id).
5. `social.chat.post` — отправить сообщение в канал (channel_id, text).
6. `social.merge_feed` — агрегировать активность (posts, friend events, channel updates).

---

## 4. Рецепты и эндпоинты

- **Рецепты (пошаговые сценарии):**
  - `GET /mcp/recipes` — список всех рецептов (фильтры: category, difficulty, search).
  - `GET /mcp/recipes/{recipe_id}` — полное описание рецепта и шагов.
  - `POST /mcp/recipes/{recipe_id}/validate` — проверка прогресса и следующий шаг.
  - Доступные id: `card_game_basic`, `saas_trial_system`, `apply_custom_buff_to_user`, `**project_economy`** (проект + валюты + кошельки).
- **Как выполнить рецепт:** вызвать шаги по очереди через `POST /mcp` (agentstack.execute) с `context: { project_id, user_id }`, подставляя в params значения из предыдущих шагов (например `{"from": "s0.result.project_id"}`).
- **Workflows (примеры цепочек):**  
`GET /mcp/ai_prompt/workflows` — возвращает get_common_workflows() с примерами и синергиями.
- **Полный список действий:**  
`GET /mcp/actions` — все допустимые action для agentstack.execute.
- **Discovery:**  
`GET /mcp/discovery` — протокол, один инструмент agentstack.execute, actions_url.

---

## 5. Краткий чеклист для агента

- Нужен проект → `projects.create_project_anonymous` / `create_project`; `project_id` + настроить заголовки из top-level credentials (`user_api_key` / `session_token`, neutral `bootstrap`). **Cursor:** Connect / `/agentstack-authorize`.
- Нужны валюты → `assets.create` с `type=currency`; список — `assets.list`.
- Нужны кошельки → `wallets.create`; пополнение — `wallets.deposit`; переводы — `wallets.transfer`; баланс — `payments.get_balance`.
- Нужны триалы/подписки → `buffs.create_buff` + `logic.create` (триггер) + `buffs.apply_buff` / `apply_temporary_effect`.
- Нужны периодические задачи → `scheduler.create_task` (cron); обновление — `scheduler.update_task`; отмена — `scheduler.cancel_task`.
- Нужны автоматические правила → `logic.get_processors`, `logic.get_commands`, затем `logic.create`.
- Нужно управлять участниками → `projects.get_users`; затем `add_user`, `update_user_role`, `remove_user` (при правах).
- Универсальное изменение данных → `commands.execute` (dna_crud) по нужной target_entity.
- Нужна база знаний / поиск → `rag.collection_create` → `rag.document_add` → `rag.search`.
- Нужна память агента → `rag.memory_add` (запомнить) → `rag.memory_search` (вспомнить).
- Нужны политики доступа к полям → `data_access.get_policy` → `data_access.set_policy` → `data_access.test_mask`.
- Нужен безопасный API-ключ для агента → `apikeys.create` с `service_caps: [список_сервисов]`.
- Нужны социальные функции → `social.public.publish` (профиль) → `social.channels.register` (канал) → `social.chat.post` (сообщение) → `social.friends.request` (связи).
- Нужны роли/права → `rbac.get_roles` → `rbac.assign_role` / `rbac.revoke_role` → `rbac.check_permission`.
- Нужна аналитика → `analytics.get_usage` (лимиты, использование) → `analytics.get_metrics` (метрики проекта).

## 6. Промпты для использования с MCP

Все промпты доступны через `GET /mcp/prompts/list` и `GET /mcp/prompts/get?name=<prompt_name>`:


| Промпт                           | Назначение                                 |
| -------------------------------- | ------------------------------------------ |
| `agentstack_system_instructions` | Системные инструкции (загружать первым)    |
| `agentstack_commerce_flows`      | Платежи, кошельки, ассеты, маркетплейс     |
| `agentstack_api_key_safety`      | service_caps, безопасные ключи для агентов |
| `agentstack_project_setup`       | Пошаговый бутстрап нового проекта          |
| `agentstack_business_flows`      | Краткая карта 12 бизнес-сценариев          |


Use-case промпты (`GET /mcp/ai_prompt/for_use_case/{use_case}`):

- `game` — игровая экономика, валюты, матчмейкинг
- `saas` — триалы, подписки, биллинг
- `ecommerce` — корзина, checkout, инвентарь
- `social` — граф, чат, публикации, приватность
- `backend_api` — RBAC, FAP, планировщик, автоматизация
- `project_setup` — полный бутстрап и массовая конфигурация
