# MCP: синергии, пробелы и инструкции

Единый справочник по возможностям MCP, сочетанию инструментов и рекомендуемым сценариям.

**Полный набор флоу и steps:** см. [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) — все JSON-RPC методы, ресурсы (list → subscribe → SSE → read), комбинации инструментов с пошаговыми таблицами и цепочки (TOOL_CHAINS).

---

## 1. Что покрыто, чего не хватает

### Полностью покрыто (CRUD / полное управление при наличии прав)

| Домен | Инструменты | Примечание |
|-------|-------------|------------|
| **Проекты** | projects.get_projects, get_project, get_stats, get_users, create_project, create_project_anonymous, update_project, delete_project | — |
| **Участники** | projects.get_users, add_user, update_user_role, remove_user | add/remove — Professional или экосистема |
| **Планировщик** | scheduler.create_task, get_task, update_task, list_tasks, execute_task, cancel_task, delete_pool_task, clear_pool_tasks, refresh_pool, get_all_db_tasks | — |
| **Ассеты** | assets.create, get, list, update, delete | В т.ч. валюты: type=currency |
| **Баффы** | buffs.create_buff, get_buff, list_active_buffs, get_effective_limits, apply_buff, extend_buff, revert_buff, cancel_buff, apply_temporary_effect, apply_persistent_effect | — |
| **Логика** | logic.create, update, delete, get, list, execute, get_processors, get_commands, flush_batch | — |
| **Кошельки** | wallets.list, wallets.create, wallets.deposit, wallets.transfer | + payments.get_balance |
| **Платежи** | payments.create, get, list_transactions, get_balance, refund | — |
| **API-ключи** | apikeys.create, list, delete | — |
| **Данные (универсально)** | commands.execute, commands.execute_batch | dna_crud по сущностям |
| **Аутентификация** | auth.login, register, get_profile, update_profile | — |
| **Аналитика** | analytics.get_usage, get_metrics | чтение метрик |
| **Процессоры** | processors.list, get_metadata, execute | для логики |

### Частично или не в MCP

| Область | Статус | Как обойти |
|---------|--------|------------|
| **Webhooks** | В API есть, в MCP нет отдельных tools | Через commands.execute или будущие webhooks.* |
| **Уведомления (send)** | В примерах упоминается notifications.send_notification | Пока использовать logic + внешние вызовы или commands |
| **Экспорт аналитики** | analytics.export_data в примерах | Использовать analytics.get_metrics, get_usage |

---

## 2. Синергии (как сочетать инструменты)

### Цепочки по смыслу

- **Проект → экономика**  
  `projects.create_project_anonymous` → `assets.create` (type: currency) → `wallets.create` → `wallets.deposit`  
  Сначала проект, потом валюты как ассеты, затем кошельки и пополнение.

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
  `commands.execute` (dna_crud: get/create/update/delete) по data_projects_project, data_projects_user, data_users_user и др.  
  Любые данные при наличии прав; валюты и конфиг проекта — через ассеты и project.data.

### Парадигмы (из AI prompt)

- **Баффы = таймеры с хуками**: on_apply, on_expire, revert_on_expire; не только подписки, но и отложенные действия, кулдауны, авто-очистка.
- **Пользователи = контейнеры**: data_users_user для NPC, сессий, ботов, джобов; идентификация по email/user_id.
- **Проект = глобальное состояние**: project.data виден всем; бафф на проект меняет правила для всех.

---

## 3. Рекомендуемый порядок операций

### Новый проект с экономикой

1. `projects.create_project_anonymous` (или create_project) — сохранить project_id и user_api_key.
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

---

## 4. Рецепты и эндпоинты

- **Рецепты (пошаговые сценарии):**
  - `GET /mcp/recipes` — список всех рецептов (фильтры: category, difficulty, search).
  - `GET /mcp/recipes/{recipe_id}` — полное описание рецепта и шагов.
  - `POST /mcp/recipes/{recipe_id}/validate` — проверка прогресса и следующий шаг.
  - Доступные id: `card_game_basic`, `saas_trial_system`, `apply_custom_buff_to_user`, **`project_economy`** (проект + валюты + кошельки).
- **Как выполнить рецепт:** вызвать шаги по очереди через `POST /mcp` (agentstack.execute) с `context: { project_id, user_id }`, подставляя в params значения из предыдущих шагов (например `{"from": "s0.result.project_id"}`).

- **Workflows (примеры цепочек):**  
  `GET /mcp/ai_prompt/workflows` — возвращает get_common_workflows() с примерами и синергиями.

- **Полный список действий:**  
  `GET /mcp/actions` — все допустимые action для agentstack.execute.

- **Discovery:**  
  `GET /mcp/discovery` — протокол, один инструмент agentstack.execute, actions_url.

---

## 5. Краткий чеклист для агента

- Нужен проект → projects.create_project_anonymous / create_project; сохранить user_api_key и project_id.
- Нужны валюты → assets.create с type=currency; список — assets.list.
- Нужны кошельки → wallets.create; пополнение — wallets.deposit; переводы — wallets.transfer; баланс — payments.get_balance.
- Нужны триалы/подписки → buffs.create_buff + logic.create (триггер) + buffs.apply_buff / apply_temporary_effect.
- Нужны периодические задачи → scheduler.create_task (cron); обновление — scheduler.update_task; отмена — scheduler.cancel_task.
- Нужны автоматические правила → logic.get_processors, logic.get_commands, затем logic.create.
- Нужно управлять участниками → projects.get_users; затем add_user, update_user_role, remove_user (при правах).
- Универсальное изменение данных → commands.execute (dna_crud) по нужной target_entity.
