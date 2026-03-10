# MCP Server - Полное описание возможностей

## 📋 Содержание

1. [Быстрый старт](#быстрый-старт)
2. [Настройка в Cursor](#настройка-в-cursor)
3. [Обзор](#обзор)
4. [Архитектура](#архитектура)
5. [API Endpoints](#api-endpoints)
6. [Доступные Tools](#доступные-tools)
7. [Особенности реализации](#особенности-реализации)
8. [Примеры использования](#примеры-использования)

---

## Быстрый старт

MCP доступен в облачной экосистеме **[agentstack.tech](https://agentstack.tech)** — локальный запуск сервера не требуется.

1. **Получите API ключ:** зарегистрируйтесь на [agentstack.tech](https://agentstack.tech) и создайте проект в дашборде, либо создайте анонимный проект одним запросом (см. ниже).
2. **Настройте плагин** (Cursor, Claude, VS Code или GPT): укажите MCP URL `https://agentstack.tech/mcp` и ваш API ключ. Готово — 60+ tools доступны в чате.

**Проверка доступности:**
```bash
# Список доступных tools (без ключа)
curl https://agentstack.tech/mcp/tools
```

---

## Настройка в Cursor

### Способ 1: HTTP MCP Server (рекомендуется)

Cursor поддерживает MCP серверы через HTTP. Настройте его следующим образом:

#### Шаг 1: Создайте конфигурационный файл

Создайте файл `cursor-mcp-config.json` в домашней директории или в настройках Cursor:

**Windows:**
```
%APPDATA%\Cursor\User\globalStorage\mcp-config.json
```

**macOS:**
```
~/Library/Application Support/Cursor/User/globalStorage/mcp-config.json
```

**Linux:**
```
~/.config/Cursor/User/globalStorage/mcp-config.json
```

#### Шаг 2: Добавьте конфигурацию MCP сервера

```json
{
  "mcpServers": {
    "agentstack": {
      "type": "http",
      "baseUrl": "https://agentstack.tech/mcp",
      "headers": {
        "X-API-Key": "your-api-key-here"
      },
      "tools": {
        "enabled": true,
        "autoDiscover": true
      }
    }
  }
}
```

#### Шаг 3: Перезапустите Cursor

После добавления конфигурации перезапустите Cursor, чтобы изменения вступили в силу.

### Способ 2: Через настройки Cursor UI

1. Откройте Cursor
2. Перейдите в **Settings** → **Features** → **MCP Servers**
3. Нажмите **Add Server**
4. Заполните форму:
   - **Name**: `agentstack`
   - **Type**: `HTTP`
   - **Base URL**: `https://agentstack.tech/mcp`
   - **API Key**: ваш API ключ (если требуется)
5. Сохраните настройки

### Способ 3: Использование через Chat (без настройки)

Если MCP сервер запущен, вы можете использовать его напрямую через HTTP API в чате Cursor:

```
Создай проект через MCP:
POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous
Body: {"tool": "projects.create_project_anonymous", "params": {"name": "Test Project"}}
```

### Получение API ключа

Если требуется API ключ для аутентификации:

1. **Создайте анонимный проект** (получите API ключ):
```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "My Project"
    }
  }'
```

2. **Используйте полученный `api_key`** в конфигурации Cursor

### Использование в Cursor Chat

После настройки вы можете использовать MCP tools прямо в чате Cursor:

#### Пример 1: Создание проекта

```
Создай новый проект через MCP с названием "My Test Project"
```

Cursor автоматически вызовет `projects.create_project_anonymous` с параметрами.

#### Пример 2: Получение списка проектов

```
Покажи все мои проекты через MCP
```

#### Пример 3: Управление пользователями

```
Добавь пользователя user@example.com в проект 1025 с ролью member
```

**Примечание:** Для `add_user` и `remove_user` требуется Professional подписка.

### Отладка подключения

Если MCP сервер не работает в Cursor:

1. **Проверьте доступность MCP:**
```bash
curl https://agentstack.tech/mcp/tools
```

2. **Проверьте логи Cursor:**
   - Откройте Developer Tools (Ctrl+Shift+I / Cmd+Option+I)
   - Перейдите в Console
   - Ищите ошибки подключения к MCP

3. **Проверьте конфигурацию:**
   - Убедитесь, что `baseUrl` = `https://agentstack.tech/mcp`
   - Убедитесь, что API ключ указан верно (без лишних пробелов)

### Примеры команд для Cursor

#### Создание и настройка проекта

```
Создай анонимный проект "My AI Project" через MCP и покажи API ключ
```

#### Работа с пользователями

```
Покажи всех пользователей проекта 1025 через MCP
```

#### Управление API ключами

```
Создай новый API ключ для проекта 1025 с именем "Development Key"
```

#### Получение статистики

```
Покажи статистику проекта 1025 через MCP
```

#### Просмотр активности

```
Покажи последние 10 событий активности проекта 1025
```

### Альтернатива: Использование через HTTP клиент

Если настройка MCP в Cursor не работает, вы можете использовать HTTP клиент прямо в коде:

```python
import httpx

async def create_project_via_mcp(name: str):
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "https://agentstack.tech/mcp/tools/projects.create_project_anonymous",
            json={
                "tool": "projects.create_project_anonymous",
                "params": {"name": name}
            }
        )
        return response.json()
```

MCP доступен по адресу **https://agentstack.tech/mcp**. Дополнительные переменные окружения не требуются.

### Практические примеры работы в Cursor

#### Сценарий 1: Создание проекта для тестирования

**Запрос в Cursor:**
```
Создай новый тестовый проект через MCP с названием "Test Project" и опиши, что получилось
```

**Что происходит:**
1. Cursor вызывает `projects.create_project_anonymous`
2. Получает `project_id`, `api_key`, `auth_key`
3. Показывает результат в чате

**Ответ Cursor:**
```
✅ Проект успешно создан!

Project ID: 1025
API Key: ask_abc123xyz...
Auth Key: ask_abc123xyz...

Теперь вы можете использовать этот API ключ для работы с проектом.
```

#### Сценарий 2: Прикрепление проекта к пользователю

**Запрос в Cursor:**
```
Прикрепи проект 1025 к пользователю с email user@example.com. Используй auth_key: ask_abc123xyz...
```

**Что происходит:**
1. Cursor вызывает `projects.attach_to_user`
2. Проект переходит в собственность пользователя
3. Создается новый API ключ для владельца

#### Сценарий 3: Получение информации о проекте

**Запрос в Cursor:**
```
Покажи информацию о проекте 1025: статистику, пользователей, настройки
```

**Что происходит:**
1. Cursor вызывает несколько tools:
   - `projects.get_project` - основная информация
   - `projects.get_stats` - статистика
   - `projects.get_users` - список пользователей
   - `projects.get_settings` - настройки
2. Агрегирует результаты и показывает в удобном виде

#### Сценарий 4: Управление API ключами

**Запрос в Cursor:**
```
Создай новый API ключ для проекта 1025 с именем "Production Key" и покажи его
```

**Что происходит:**
1. Cursor вызывает `projects.create_api_key`
2. Получает новый ключ
3. Показывает его (⚠️ важно сохранить, т.к. показывается только один раз)

#### Сценарий 5: Просмотр активности

**Запрос в Cursor:**
```
Покажи последние 20 событий активности проекта 1025
```

**Что происходит:**
1. Cursor вызывает `projects.get_activity` с `limit=20`
2. Показывает список событий с временными метками

### Советы по использованию

1. **Всегда сохраняйте API ключи** - они показываются только один раз при создании
2. **Используйте анонимное создание** для быстрого тестирования
3. **Прикрепляйте проекты** к реальным пользователям для production
4. **Проверяйте подписку** перед добавлением пользователей (требуется Professional)
5. **Используйте trace_id** для отладки проблем

### Troubleshooting

#### Проблема: "Tool not found"

**Решение:**
- Убедитесь, что MCP сервер запущен
- Проверьте, что tool name правильный (с точкой: `projects.create_project`)
- Проверьте конфигурацию в Cursor

#### Проблема: "Connection refused"

**Решение:**
- Проверьте, что сервер запущен на порту 8000
- Проверьте firewall настройки
- Убедитесь, что `baseUrl` в конфигурации правильный

#### Проблема: "403 Forbidden" при добавлении пользователя

**Решение:**
- Это нормально - требуется Professional подписка
- Обновите подписку пользователя или используйте другой метод

#### Проблема: "Invalid auth_key" при прикреплении

**Решение:**
- Убедитесь, что используете правильный `auth_key` из анонимного создания
- Проверьте, что проект еще не прикреплен к другому пользователю

---

## Обзор

**MCP (Microservice Control Plane)** - это универсальный сервер для управления всеми сервисами AgentStack через единый интерфейс. MCP предоставляет AI агентам (Google Studio, Cursor, и другим) полный доступ к функциональности платформы через стандартизированный набор инструментов.

### Основные характеристики

- **Версия протокола**: 2.0
- **Всего доступных tools**: 62+
- **Поддержка streaming**: Да
- **Idempotency**: Да
- **Job tracking**: Да
- **Порт**: 8000 (через API Gateway)

### Ключевые возможности

- ✅ Полное управление проектами (включая анонимное создание)
- ✅ Аутентификация и авторизация
- ✅ Управление платежами
- ✅ Планировщик задач
- ✅ Аналитика и метрики
- ✅ Управление API ключами
- ✅ Rules Engine (серверная логика)
- ✅ Webhooks
- ✅ Уведомления
- ✅ Кошельки
- ✅ Buff System (временные и постоянные эффекты)

---

## Архитектура

### Структура проекта

```
mcp/
├── main.py              # FastAPI приложение, middleware, регистрация
├── routes.py            # API endpoints для tools
├── tools.py             # Реализация всех MCP tools
├── sdk_wrapper.py       # SDK обертка для работы с проектами
└── dependencies.py      # Зависимости
```

### Компоненты

1. **FastAPI Application** (`main.py`)
   - CORS middleware
   - Metrics middleware
   - Correlation ID tracking
   - Health checks
   - Prometheus metrics

2. **Routes** (`routes.py`)
   - `GET /mcp/tools` - список всех доступных tools
   - `GET /mcp/discovery` - discovery endpoint для клиентов
   - `POST /mcp/tools/{tool_name}` - выполнение tool
   - `POST /mcp/tools/{tool_name}/stream` - streaming выполнение
   - `GET /mcp/jobs/{job_id}` - статус задачи

3. **Tools Registry** (`tools.py`)
   - Все доступные tools зарегистрированы в `MCP_TOOLS`
   - Pydantic модели для валидации запросов
   - Обработка ошибок и логирование

4. **SDK Wrapper** (`sdk_wrapper.py`)
   - Обертка над HTTP API для работы с проектами
   - Использует существующие endpoints из `agentstack-core`
   - Единообразный интерфейс для всех операций

---

## API Endpoints

**Публичные эндпоинты (без X-API-Key):** вызовы без заголовка аутентификации допускаются для `GET /mcp/tools` (список tools) и для вызова инструмента `projects.create_project_anonymous` (создание анонимного проекта и получение ключей). Все остальные запросы требуют `X-API-Key`.

### Discovery и метаданные

#### `GET /mcp/tools`
Возвращает список всех доступных tools, сгруппированных по категориям. **Может вызываться без X-API-Key.**

**Ответ:**
```json
{
  "tools": {
    "auth": [...],
    "payments": [...],
    "projects": [...],
    ...
  },
  "version": "2.0",
  "total_tools": 60,
  "description": "Full AgentStack MCP integration..."
}
```

#### `GET /mcp/discovery`
Discovery endpoint для автоматического обнаружения возможностей.

**Ответ:**
```json
{
  "protocol_version": "2.0",
  "capabilities": {
    "streaming": true,
    "idempotency": true,
    "job_tracking": true
  },
  "services": {
    "auth": {...},
    "projects": {...},
    ...
  }
}
```

### Выполнение tools

#### `POST /mcp/tools/{tool_name}`
Выполняет указанный tool.

**Запрос:**
```json
{
  "tool": "projects.create_project",
  "params": {
    "name": "My Project",
    "description": "Project description"
  },
  "idempotency_key": "optional-key"
}
```

**Ответ:**
```json
{
  "success": true,
  "data": {...},
  "error": null,
  "trace_id": "uuid"
}
```

#### `POST /mcp/tools/{tool_name}/stream`
Выполняет tool с streaming ответом (Server-Sent Events).

**Ответ:** SSE stream с событиями:
- `status: started`
- `status: processing`
- `status: completed`
- `status: error`

#### `GET /mcp/jobs/{job_id}`
Получает статус асинхронной задачи.

---

## Доступные Tools

### 🔐 Auth Tools (4 tools)

#### `auth.quick_auth`
Быстрая аутентификация пользователя.

**Параметры:**
- `email` (string) - Email пользователя
- `password` (string) - Пароль

**Возвращает:** Токен доступа и информацию о пользователе

#### `auth.create_user`
Создание нового пользователя.

**Параметры:**
- `email` (string)
- `password` (string)
- `display_name` (string, optional)

#### `auth.assign_role`
Назначение роли пользователю.

**Параметры:**
- `user_id` (int)
- `role` (string)
- `project_id` (int, optional)

#### `auth.get_profile`
Получение профиля пользователя.

#### `auth.update_profile`
Обновление профиля пользователя.

---

### ⚙️ Logic Engine Tools (9 tools)

#### `logic.create`
Создание правила логики.

#### `logic.update`
Обновление правила логики.

#### `logic.delete`
Удаление правила логики.

#### `logic.get`
Получение информации о правиле.

#### `logic.list`
Список всех правил проекта.

#### `logic.execute`
Выполнение правила логики.

#### `logic.get_processors`
Получение списка доступных процессоров.

#### `logic.get_commands`
Получение списка доступных команд.

#### `logic.flush_batch`
Очистка батч-очереди правил.

---

### 💳 Payments Tools (4 tools)

#### `payments.create_payment`
Создание платежа.

**Параметры:**
- `amount` (float)
- `currency` (string)
- `payment_method` (string)
- `description` (string, optional)

#### `payments.get_status`
Получение статуса платежа.

**Параметры:**
- `payment_id` (string)

#### `payments.refund`
Возврат платежа.

**Параметры:**
- `payment_id` (string)
- `amount` (float, optional) - частичный возврат

#### `payments.list_transactions`
Список транзакций.

**Параметры:**
- `limit` (int, optional)
- `offset` (int, optional)

#### `payments.get_balance`
Получение баланса.

---

### 📁 Projects Tools (8 tools)

#### Основные CRUD операции

##### `projects.get_projects`
Получение списка проектов.

**Параметры:**
- `is_active` (bool, optional)
- `limit` (int, optional)
- `offset` (int, optional)

##### `projects.get_project`
Получение проекта по ID.

**Параметры:**
- `project_id` (int)

##### `projects.create_project`
Создание проекта (требует авторизацию).

**Параметры:**
- `name` (string) - Обязательно
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `preset_id` (string, optional)
- `settings` (dict, optional)

##### `projects.create_project_anonymous`
**⭐ НОВОЕ:** Создание проекта без авторизации (для AI агентов).

**Параметры:**
- `name` (string) - Обязательно
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `preset_id` (string, optional)
- `settings` (dict, optional)

**Возвращает:**
```json
{
  "project_id": 1025,
  "user_id": 1037,
  "user_api_key": "anon_ask_...",
  "project_api_key": "ask_...",
  "session_token": "...",
  "project": {...},
  "_auth_info": {...}
}
```

**Особенности:**
- Не требует авторизации
- **Автоматически создает анонимного пользователя** с `is_anonymous: true`
- Генерирует API ключ с префиксом `anon_ask_` (ANONYMOUS tier)
- Возвращает `user_api_key`, `project_api_key`, `session_token`, `user_id`
- Пользователь сразу аутентифицирован и готов к использованию

**⚠️ ОГРАНИЧЕНИЯ ANONYMOUS ТАРИФА:**
- **1 проект максимум** - Нельзя создавать дополнительные проекты
- **1 API ключ** - Нельзя создавать дополнительные ключи
- **1,000 вызовов Logic Engine/месяц** (вместо 10,000 у FREE)
- **20 MB JSON хранилища** (вместо 100 MB у FREE)
- **20 триггеров** (вместо 50 у FREE)
- **Платежи отключены** - Нельзя обрабатывать платежи

**Конвертация в полный аккаунт:**
- Используйте `auth.convert_anonymous_user` для конвертации в FREE тариф
- После конвертации все лимиты увеличиваются до FREE tier
- Пользователь может войти с email/password
- Можно создавать дополнительные проекты и API ключи

##### `projects.update_project`
Обновление проекта.

**Параметры:**
- `project_id` (int)
- `name` (string, optional)
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `is_active` (bool, optional)

##### `projects.delete_project`
Удаление проекта.

**Параметры:**
- `project_id` (int)

#### Статистика и аналитика

##### `projects.get_stats`
Получение статистики проекта.

**Параметры:**
- `project_id` (int)

**Возвращает:** Статистику по проекту (пользователи, активность, использование)

#### Управление пользователями

##### `projects.get_users`
Получение списка пользователей проекта.

**Параметры:**
- `project_id` (int)
- `is_active` (bool, optional)
- `role` (string, optional)
- `limit` (int, optional)
- `offset` (int, optional)

##### `projects.add_user`
**⭐ ТРЕБУЕТ Professional подписку**

Добавление пользователя в проект.

**Параметры:**
- `project_id` (int)
- `user_id` (int, optional)
- `email` (string, optional)
- `role` (string) - По умолчанию "member"

**Ограничения:**
- Требуется Professional подписка (pro, professional, enterprise)
- Проверка на уровне API endpoint
- Ошибка 403 при отсутствии подписки

##### `projects.remove_user`
**⭐ ТРЕБУЕТ Professional подписку**

Удаление пользователя из проекта.

**Параметры:**
- `project_id` (int)
- `user_id` (int)

##### `projects.update_user_role`
Обновление роли пользователя в проекте.

**Параметры:**
- `project_id` (int)
- `user_id` (int)
- `role` (string)

##### `projects.attach_to_user`
**⭐ НОВОЕ:** Прикрепление анонимного проекта к пользователю.

**Параметры:**
- `project_id` (int)
- `auth_key` (string) - Ключ из анонимного создания
- `user_id` (int, optional)
- `email` (string, optional)

**Процесс:**
1. Проверяет соответствие `auth_key` API ключам проекта
2. Обновляет `project.user_id` на нового владельца
3. Создает записи в `data_projects_user`
4. Удаляет старый анонимный ключ
5. Создает новый API ключ для владельца

**Возвращает:**
```json
{
  "success": true,
  "project_id": 1025,
  "new_api_key": "ask_...",
  "message": "Project successfully attached to user"
}
```

#### Управление API ключами

##### `projects.get_api_keys`
Получение списка API ключей проекта.

**Параметры:**
- `project_id` (int)

**Использует:** `GET /api/apikeys/keys?project_id={project_id}`

##### `projects.create_api_key`
Создание API ключа для проекта.

**Параметры:**
- `project_id` (int)
- `name` (string)
- `scopes` (array, optional)
- `expires_at` (string, optional)

**Использует:** `POST /api/apikeys/keys` с `project_id` в body

##### `projects.delete_api_key`
Удаление API ключа проекта.

**Параметры:**
- `project_id` (int)
- `key_id` (string)

**Использует:** `DELETE /api/apikeys/keys/{key_id}?project_id={project_id}`

#### Настройки проекта

##### `projects.get_settings`
Получение настроек проекта (извлекает `config`).

**Параметры:**
- `project_id` (int)

##### `projects.update_settings`
Обновление настроек проекта (обновляет `config`).

**Параметры:**
- `project_id` (int)
- `settings` (dict)

#### Активность проекта

##### `projects.get_activity`
Получение активности проекта (логи).

**Параметры:**
- `project_id` (int)
- `limit` (int, optional)
- `offset` (int, optional)

**Использует:** `GET /api/projects/{project_id}/logs`

---

### ⏰ Scheduler Tools (11 tools)

#### `scheduler.schedule_task`
Планирование задачи.

**Параметры:**
- `project_id` (int)
- `run_at` (string) - ISO datetime
- `tool` (string)
- `payload` (dict)

#### `scheduler.cancel_task`
Отмена задачи.

**Параметры:**
- `task_id` (string)

#### `scheduler.get_task`
Получение информации о задаче.

**Параметры:**
- `task_id` (string)

#### `scheduler.list_tasks`
Список задач.

**Параметры:**
- `project_id` (int, optional)

#### `scheduler.update_task`
Обновление задачи.

**Параметры:**
- `task_id` (string)
- `task_data` (dict)

---

### 📊 Analytics Tools (2 tools)

#### `analytics.get_usage`
Получение статистики использования.

**Параметры:**
- `project_id` (int, optional)
- `start_date` (string, optional)
- `end_date` (string, optional)

#### `analytics.set_budget`
Установка бюджета.

**Параметры:**
- `project_id` (int)
- `budget` (float)
- `period` (string) - "monthly", "yearly"

#### `analytics.get_metrics`
Получение метрик.

**Параметры:**
- `project_id` (int, optional)
- `metric_type` (string, optional)

#### `analytics.export_data`
Экспорт данных аналитики.

**Параметры:**
- `project_id` (int)
- `format` (string) - "json", "csv"
- `start_date` (string, optional)
- `end_date` (string, optional)

#### `analytics.get_dashboard`
Получение данных для дашборда.

**Параметры:**
- `project_id` (int, optional)

---

### 🔑 API Keys Tools (3 tools)

#### `api_keys.create_key`
Создание API ключа.

**Параметры:**
- `project_id` (int)
- `name` (string)
- `scopes` (array, optional)

#### `api_keys.revoke_key`
Отзыв API ключа.

**Параметры:**
- `key_id` (string)

#### `api_keys.list_keys`
Список API ключей.

**Параметры:**
- `project_id` (int, optional)

#### `api_keys.update_key`
Обновление API ключа.

**Параметры:**
- `key_id` (string)
- `key_data` (dict)

#### `api_keys.get_usage`
Использование API ключа.

**Параметры:**
- `key_id` (string)

---

### ⚙️ Rules Tools (6 tools)

#### `rules.create_rules`
Создание правил.

**Параметры:**
- `project_id` (int)
- `json_rules` (dict)
- `name` (string, optional)
- `description` (string, optional)

#### `rules.test_rules`
Тестирование правил.

**Параметры:**
- `rule_id` (string)
- `test_data` (dict)

#### `rules.activate_rules`
Активация правил.

**Параметры:**
- `rule_id` (string)

#### `rules.list_rules`
Список правил.

**Параметры:**
- `project_id` (int, optional)

#### `rules.update_rules`
Обновление правил.

**Параметры:**
- `rule_id` (string)
- `rule_data` (dict)

#### `rules.delete_rules`
Удаление правил.

**Параметры:**
- `rule_id` (string)

---

### 🔔 Webhooks Tools (5 tools)

#### `webhooks.create_endpoint`
Создание webhook endpoint.

**Параметры:**
- `project_id` (int)
- `url` (string)
- `events` (array)
- `secret` (string, optional)

#### `webhooks.list_endpoints`
Список webhook endpoints.

**Параметры:**
- `project_id` (int, optional)

#### `webhooks.update_endpoint`
Обновление webhook endpoint.

**Параметры:**
- `endpoint_id` (string)
- `endpoint_data` (dict)

#### `webhooks.delete_endpoint`
Удаление webhook endpoint.

**Параметры:**
- `endpoint_id` (string)

#### `webhooks.test_endpoint`
Тестирование webhook endpoint.

**Параметры:**
- `endpoint_id` (string)
- `test_data` (dict, optional)

---

### 📬 Notifications Tools (4 tools)

#### `notifications.send_notification`
Отправка уведомления.

**Параметры:**
- `project_id` (int)
- `user_id` (int, optional)
- `type` (string)
- `message` (string)
- `data` (dict, optional)

#### `notifications.list_notifications`
Список уведомлений.

**Параметры:**
- `project_id` (int)
- `user_id` (int, optional)
- `limit` (int, optional)

#### `notifications.create_template`
Создание шаблона уведомления.

**Параметры:**
- `project_id` (int)
- `name` (string)
- `template` (string)
- `variables` (array, optional)

#### `notifications.update_template`
Обновление шаблона уведомления.

**Параметры:**
- `template_id` (string)
- `template_data` (dict)

---

### 💰 Wallets Tools (4 tools)

#### `wallets.get_balance`
Получение баланса кошелька.

**Параметры:**
- `project_id` (int)
- `wallet_id` (int, optional)

#### `wallets.list_transactions`
Список транзакций кошелька.

**Параметры:**
- `project_id` (int)
- `wallet_id` (int, optional)
- `limit` (int, optional)

#### `wallets.create_wallet`
Создание кошелька.

**Параметры:**
- `project_id` (int)
- `name` (string)
- `type` (string) - "business", "personal"
- `currency` (string)

#### `wallets.update_wallet`
Обновление кошелька.

**Параметры:**
- `wallet_id` (int)
- `wallet_data` (dict)

---

### 🎁 Buff Management Tools (10 tools)

**Что такое бафы?** Бафы (Buffs) - это система временных и постоянных эффектов, которые изменяют лимиты, функции и ресурсы пользователей и проектов. 

**Временные эффекты (автоматически истекают и откатываются):**
- Триальные периоды (7-30 дней) - бесплатные пробные версии функций
- Промо-акции и события - ограниченные по времени предложения
- Временные бонусы - дополнительные ресурсы на короткий срок

**Постоянные эффекты (не истекают, требуют ручного управления):**
- Подписки - ежемесячные/годовые планы с возможностью продления
- Разовые покупки - одноразовые улучшения (например, увеличение лимитов)
- Пакеты улучшений - накопительные постоянные бонусы

Система баффов идеально подходит для управления триальными периодами, промо-акциями, подписками и разовыми покупками в вашем SaaS, игре или приложении.

#### `buffs.create_buff`
Создание шаблона баффа в состоянии PENDING.

**Параметры:**
- `entity_id` (int, required) - ID сущности (user или project)
- `entity_kind` (string, required) - Тип сущности: "user" или "project"
- `name` (string, required) - Название баффа
- `type` (string, required) - Тип баффа (trial, promo, subscription, purchase)
- `category` (string, required) - Категория для фильтрации
- `duration_days` (int, optional, default: 30) - Длительность в днях
- `priority` (int, optional, default: 100) - Приоритет (0-3000)
- `effects` (object, optional) - Эффекты в формате {"data.limits.api_calls": 10000}
- `config` (object, optional) - Конфигурация (persistent, revert_on_expire, etc.)
- `project_id` (int, optional) - ID проекта (для user buffs)

**Пример:**
```json
{
  "tool": "buffs.create_buff",
  "params": {
    "entity_id": 1,
    "entity_kind": "user",
    "name": "7-Day Premium Trial",
    "type": "trial",
    "category": "subscription",
    "duration_days": 7,
    "effects": {
      "data.limits.api_calls": 10000
    }
  }
}
```

#### `buffs.apply_buff`
Применение баффа к сущности (активация PENDING баффа).

**Параметры:**
- `buff_id` (string, required) - UUID баффа
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `project_id` (int, optional) - ID проекта

**Пример:**
```json
{
  "tool": "buffs.apply_buff",
  "params": {
    "buff_id": "uuid-here",
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

#### `buffs.extend_buff`
Продление активного баффа.

**Параметры:**
- `buff_id` (string, required) - UUID баффа
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `additional_days` (int, optional) - Дополнительные дни (если не указано, продлевает на исходную длительность)
- `project_id` (int, optional) - ID проекта

**Пример:**
```json
{
  "tool": "buffs.extend_buff",
  "params": {
    "buff_id": "uuid-here",
    "entity_id": 123,
    "entity_kind": "user",
    "additional_days": 30
  }
}
```

#### `buffs.revert_buff`
Откат активного баффа с валидацией успешности.

**Параметры:**
- `buff_id` (string, required) - UUID баффа
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `project_id` (int, optional) - ID проекта

**Важно:** Откат проверяет что ресурсы могут быть восстановлены. Если бафф добавил 100 золота, при откате должно быть отнято 100 золота. Если откат невозможен (недостаточно ресурсов), выбрасывается ошибка.

**Пример:**
```json
{
  "tool": "buffs.revert_buff",
  "params": {
    "buff_id": "uuid-here",
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

#### `buffs.cancel_buff`
Принудительная отмена баффа в любом состоянии.

**Параметры:**
- `buff_id` (string, required) - UUID баффа
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `project_id` (int, optional) - ID проекта

**Важно:** 
- Для PENDING баффов - обычные права
- Для ACTIVE баффов - требуются admin/owner права
- Для ACTIVE баффов сначала выполняется revert, затем удаление

**Пример:**
```json
{
  "tool": "buffs.cancel_buff",
  "params": {
    "buff_id": "uuid-here",
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```

#### `buffs.get_buff`
Получение информации о конкретном баффе.

**Параметры:**
- `buff_id` (string, required) - UUID баффа
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `project_id` (int, optional) - ID проекта

**Возвращает:** Полную информацию о баффе включая состояние, эффекты, конфигурацию, сроки действия.

#### `buffs.list_active_buffs`
Список активных баффов для сущности.

**Параметры:**
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `category` (string, optional) - Фильтр по категории
- `project_id` (int, optional) - ID проекта
- `include_ecosystem` (bool, optional) - Включать экосистемные баффы (для проектов)

**Пример:**
```json
{
  "tool": "buffs.list_active_buffs",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "category": "subscription",
    "project_id": 1
  }
}
```

#### `buffs.get_effective_limits`
Получение эффективных лимитов с учетом всех активных баффов.

**Параметры:**
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `project_id` (int, optional) - ID проекта

**Возвращает:** Эффективные лимиты после применения всех активных баффов.

**Пример:**
```json
{
  "tool": "buffs.get_effective_limits",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

#### `buffs.apply_temporary_effect`
Быстрое применение временного эффекта (создает и применяет в одном шаге).

**Параметры:**
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `name` (string, required) - Название эффекта
- `duration_days` (int, required) - Длительность в днях
- `effects` (object, required) - Эффекты
- `priority` (int, optional, default: 100) - Приоритет
- `project_id` (int, optional) - ID проекта

**Пример:**
```json
{
  "tool": "buffs.apply_temporary_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Weekend Bonus",
    "duration_days": 2,
    "effects": {
      "data.limits.api_calls": 5000
    }
  }
}
```

#### `buffs.apply_persistent_effect`
Быстрое применение постоянного эффекта (создает и применяет в одном шаге).

**Параметры:**
- `entity_id` (int, required) - ID сущности
- `entity_kind` (string, required) - Тип сущности
- `name` (string, required) - Название эффекта
- `effects` (object, required) - Эффекты
- `priority` (int, optional, default: 300) - Приоритет
- `project_id` (int, optional) - ID проекта

**Пример:**
```json
{
  "tool": "buffs.apply_persistent_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Lifetime Premium",
    "effects": {
      "data.features.premium": true,
      "data.limits.api_calls": 1000000
    }
  }
}
```

### Workflows и комбинации инструментов

**Workflow 1: Полный цикл триального периода**
```json
[
  {"tool": "buffs.create_buff", "params": {...}},
  {"tool": "buffs.apply_buff", "params": {"buff_id": "<from previous>"}},
  {"tool": "buffs.list_active_buffs", "params": {...}}
]
```

**Workflow 2: Продление подписки**
```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.extend_buff", "params": {"buff_id": "<subscription_buff_id>"}},
  {"tool": "buffs.get_buff", "params": {"buff_id": "<subscription_buff_id>"}}
]
```

**Workflow 3: Отмена ошибочного баффа**
```json
[
  {"tool": "buffs.get_buff", "params": {...}},
  {"tool": "buffs.revert_buff", "params": {...}},
  {"tool": "buffs.cancel_buff", "params": {...}}
]
```

### Сценарии использования

**Что такое бафы?** Бафы (Buffs) - это система временных и постоянных эффектов, которые изменяют лимиты, функции и ресурсы пользователей и проектов. Идеально подходят для управления триалами, промо-акциями, подписками и разовыми покупками.

**Временные эффекты (автоматически истекают и откатываются):**
- **Триальные периоды (7-30 дней)** - бесплатные пробные версии функций для новых пользователей
- **Промо-акции и события** - ограниченные по времени предложения (например, "Черная пятница")
- **Временные бонусы** - дополнительные ресурсы на короткий срок (например, удвоение опыта на выходных)
- **Сезонные предложения** - праздничные акции и специальные события

**Постоянные эффекты (не истекают, требуют ручного управления):**
- **Подписки (с продлением)** - ежемесячные/годовые планы с возможностью автоматического продления
- **Разовые покупки** - одноразовые улучшения (например, увеличение лимитов API calls)
- **Пакеты улучшений** - накопительные постоянные бонусы (можно купить несколько раз)
- **Lifetime upgrades** - пожизненные улучшения без срока действия

**Примеры синергии с другими системами:**
- **Buffs + Logic Engine** - автоматическое применение триалов при регистрации пользователя
- **Buffs + Payments** - продажа подписок и разовых покупок через платежную систему
- **Buffs + Scheduler** - автоматическое продление подписок по расписанию
- **Buffs + Analytics** - отслеживание эффективности промо-кампаний и конверсии триалов
- **Buffs + Notifications** - уведомления о новых бафах, истечении триалов и промо-акциях

**Подробные примеры комплексных проектов:** См. [examples/mcp_complex_projects.md](examples/mcp_complex_projects.md), [examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md), [examples/mcp_buffs_temporary.md](examples/mcp_buffs_temporary.md), [examples/mcp_buffs_persistent.md](examples/mcp_buffs_persistent.md).

---

## Особенности реализации

### 1. Анонимное создание проектов

**Проблема:** AI агенты (Google Studio, Cursor) не могут создавать проекты без предварительной регистрации пользователя.

**Решение:** `projects.create_project_anonymous`

- ✅ Не требует авторизации
- ✅ **Автоматически создает анонимного пользователя** с `is_anonymous: true`
- ✅ Генерирует API ключ с префиксом `anon_ask_` (ANONYMOUS tier)
- ✅ Возвращает `user_api_key`, `project_api_key`, `session_token`, `user_id`
- ✅ Пользователь сразу аутентифицирован и готов к использованию
- ✅ Позволяет сразу начать работу с проектом

**⚠️ Ограничения ANONYMOUS тарифа:**
- 1 проект максимум (нельзя создавать дополнительные)
- 1 API ключ (нельзя создавать дополнительные)
- 1,000 вызовов Logic Engine/месяц (вместо 10,000 у FREE)
- 20 MB JSON хранилища (вместо 100 MB у FREE)
- 20 триггеров (вместо 50 у FREE)
- Платежи отключены

**Конвертация:**
- Используйте `auth.convert_anonymous_user` для конвертации в FREE тариф
- После конвертации все лимиты увеличиваются до FREE tier

**Использование:**
```json
{
  "tool": "projects.create_project_anonymous",
  "params": {
    "name": "Test Project",
    "description": "Created by AI agent"
  }
}
```

### 2. Прикрепление проектов к пользователям

**Проблема:** Анонимные проекты нужно привязать к реальному пользователю.

**Решение:** `projects.attach_to_user`

- ✅ Требует авторизацию (user_id нового владельца)
- ✅ Проверяет `auth_key` из анонимного создания
- ✅ Transfer ownership:
  - Обновляет `project.user_id`
  - Создает записи в `data_projects_user`
  - Удаляет старый анонимный ключ
  - Создает новый ключ для владельца

**Использование:**
```json
{
  "tool": "projects.attach_to_user",
  "params": {
    "project_id": 1025,
    "auth_key": "ask_...",
    "user_id": 123
  }
}
```

### 3. Ограничения по подпискам

**Управление пользователями проектов** (`add_user`, `remove_user`) требует **Professional подписку**.

**Реализация:**
- Проверка на уровне API endpoint (`agentstack-core/endpoints/projects_endpoints.py`)
- Использует `SubscriptionService.get_user_subscription()`
- Проверяет `plan_type` в ['pro', 'professional', 'enterprise']
- Возвращает 403 Forbidden при отсутствии подписки
- Ошибка пробрасывается через MCP без изменений

**Ошибка:**
```json
{
  "success": false,
  "error": "HTTP 403: Professional subscription required for adding/removing project users. Please upgrade your subscription."
}
```

### 4. SDK Wrapper

**Архитектура:**
- `ProjectsSDKWrapper` - обертка над HTTP API
- Использует `shared.clients.http.request` для запросов
- Единообразный интерфейс для всех операций
- Автоматическое построение заголовков (Authorization, X-Project-ID)
- Использует существующие endpoints (не дублирует функциональность)

**Особенности:**
- API ключи управляются через `/api/apikeys/keys` (не дубликаты)
- Настройки извлекаются из `config` проекта
- Активность получается из `/api/projects/{id}/logs`

### 5. Обработка ошибок

**Стратегия:**
- Ошибки от SDK/API пробрасываются напрямую
- HTTP статусы не конвертируются
- Сохранение `trace_id` для отладки
- Использование `httpx.HTTPStatusError` для HTTP ошибок

**Формат ошибки:**
```json
{
  "success": false,
  "error": "HTTP 403: Insufficient permissions",
  "trace_id": "uuid"
}
```

### 6. Валидация запросов

**Pydantic модели:**
- Все новые методы проектов используют Pydantic модели
- Автоматическая валидация параметров
- Type-safe интерфейс

**Пример:**
```python
class CreateProjectRequest(BaseModel):
    name: str
    description: Optional[str] = None
    config: Optional[Dict[str, Any]] = None
    ...
```

### 7. Демо режим

**Поддержка:**
- Проверка `demo_context.is_readonly()`
- Блокировка write операций в read-only режиме
- Проверка capabilities через `DemoCapabilities`

---

## Примеры использования

### Пример 1: Анонимное создание проекта

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "My AI Project",
      "description": "Created by AI agent"
    }
  }'
```

**Ответ:**
```json
{
  "success": true,
  "data": {
    "project_id": 1025,
    "api_key": "ask_abc123...",
    "auth_key": "ask_abc123...",
    "project": {
      "id": 1025,
      "name": "My AI Project",
      ...
    }
  },
  "trace_id": "uuid"
}
```

### Пример 2: Прикрепление проекта к пользователю

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.attach_to_user \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.attach_to_user",
    "params": {
      "project_id": 1025,
      "auth_key": "ask_abc123...",
      "user_id": 123
    }
  }'
```

**Ответ:**
```json
{
  "success": true,
  "data": {
    "success": true,
    "project_id": 1025,
    "new_api_key": "ask_xyz789...",
    "message": "Project 1025 successfully attached to user 123"
  },
  "trace_id": "uuid"
}
```

### Пример 3: Получение списка проектов

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.get_projects \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.get_projects",
    "params": {
      "is_active": true,
      "limit": 10
    }
  }'
```

### Пример 4: Добавление пользователя (требует Professional)

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.add_user \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.add_user",
    "params": {
      "project_id": 1025,
      "email": "user@example.com",
      "role": "member"
    }
  }'
```

**При отсутствии подписки:**
```json
{
  "success": false,
  "error": "HTTP 403: Professional subscription required for adding/removing project users. Please upgrade your subscription.",
  "trace_id": "uuid"
}
```

### Пример 5: Streaming выполнение

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project/stream \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.create_project",
    "params": {
      "name": "Streaming Project"
    }
  }'
```

**Ответ (SSE):**
```
data: {"status": "started", "trace_id": "uuid"}

data: {"status": "processing", "progress": 50}

data: {"status": "completed", "result": {...}}
```

---

## Итоговая статистика

### По категориям

- **Auth**: 4 tools
- **Projects**: 8 tools
- **Logic Engine**: 9 tools
- **Processors**: 3 tools
- **Commands**: 2 tools
- **Payments**: 4 tools
- **Scheduler**: 11 tools ⭐ (самая большая категория)
- **Analytics**: 2 tools
- **API Keys**: 3 tools
- **Buffs**: 10 tools
- **Assets**: 4 tools

**Всего: 60+ tools**

### Новые возможности (последнее обновление)

1. ✅ Анонимное создание проектов
2. ✅ Прикрепление проектов к пользователям
3. ✅ Полный набор методов управления проектами
4. ✅ Интеграция с существующими endpoints
5. ✅ Проверка подписки для управления пользователями

---

## Заключение

MCP сервер предоставляет **полный доступ** ко всей функциональности AgentStack через единый интерфейс. Особое внимание уделено:

- **Удобству для AI агентов** (анонимное создание)
- **Гибкости** (прикрепление проектов)
- **Безопасности** (проверка подписок, валидация)
- **Надежности** (обработка ошибок, trace_id)
- **Производительности** (streaming, кэширование)

Все tools используют существующие endpoints, что обеспечивает консистентность и отсутствие дублирования кода.

---

**Версия документа:** 1.0  
**Дата обновления:** 2025-01-29  
**Автор:** AgentStack Development Team

