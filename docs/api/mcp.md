# MCP Documentation

## 🤖 Model Context Protocol для AI агентов

AgentStack предоставляет полнофункциональный MCP (Model Context Protocol) сервер для интеграции с AI агентами. MCP позволяет AI агентам безопасно взаимодействовать с платформой через стандартизированный протокол.

## 🔧 Конфигурация

MCP доступен в облачной экосистеме **[agentstack.tech](https://agentstack.tech)**. Локальный запуск сервера не требуется.

### Базовый URL
```
https://agentstack.tech/mcp
```

### Аутентификация
```bash
# API Key в заголовке
curl -H "X-API-Key: your_mcp_api_key" https://agentstack.tech/mcp/tools
```

### Публичные эндпоинты (без X-API-Key)

Следующие эндпоинты **допускают вызов без заголовка X-API-Key** и предназначены для онбординга и получения первого ключа:

| Метод | Путь | Описание |
|-------|------|----------|
| GET | `/mcp/tools` | Список всех MCP tools (описания и схемы). Используется сканерами и клиентами до аутентификации. |
| POST | `/mcp/tools` | JSON-RPC с `method: "tools/call"` и `params.name: "projects.create_project_anonymous"` — создание анонимного проекта без ключа. Возвращает `user_api_key`, `project_id` и др. |
| POST | `/mcp/tools/projects.create_project_anonymous` | (Standalone MCP) Прямой вызов инструмента; тело `{"params": {"name": "..."}}`. Возвращает `api_key` / `user_api_key`, `project_id`. |

После получения ключа из `projects.create_project_anonymous` все остальные запросы должны отправляться с заголовком `X-API-Key: <user_api_key>`.

## 🛠️ Доступные инструменты

### 💳 Платежные инструменты

#### `create_payment`
Создание нового платежа

**Параметры**:
```json
{
  "merchant_id": 123,
  "amount_minor": 10000,
  "currency": "RUB",
  "description": "Payment description",
  "payment_method": "CARD",
  "idempotency_key": "unique_key"
}
```

**Ответ**:
```json
{
  "payment_id": "pay_123456",
  "status": "pending",
  "amount": 10000,
  "currency": "RUB",
  "payment_url": "https://checkout.example.com/pay_123456"
}
```

#### `get_payment_status`
Получение статуса платежа

**Параметры**:
```json
{
  "payment_id": "pay_123456"
}
```

**Ответ**:
```json
{
  "payment_id": "pay_123456",
  "status": "completed",
  "amount": 10000,
  "currency": "RUB",
  "created_at": "2024-01-15T10:00:00Z",
  "completed_at": "2024-01-15T10:05:00Z"
}
```

#### `pay_card`
Оплата банковской картой

**Параметры**:
```json
{
  "merchant_id": 123,
  "amount_minor": 10000,
  "currency": "RUB",
  "card_number": "4111111111111111",
  "exp_month": 12,
  "exp_year": 2025,
  "cvv": "123"
}
```

#### `pay_usdt`
Оплата в USDT

**Параметры**:
```json
{
  "merchant_id": 123,
  "amount_minor": 10000,
  "currency": "USDT",
  "network": "TRC20",
  "wallet_address": "TWalletAddress"
}
```

### 🔐 Аутентификация

#### `quick_auth`
Быстрая аутентификация пользователя

**Параметры**:
```json
{
  "user_id": 123,
  "project_id": 456,
  "permissions": ["payments:create", "analytics:read"]
}
```

**Ответ**:
```json
{
  "access_token": "jwt_token",
  "expires_in": 3600,
  "permissions": ["payments:create", "analytics:read"]
}
```

### 📊 Аналитика

#### `get_analytics`
Получение аналитических данных

**Параметры**:
```json
{
  "project_id": 123,
  "period": "week",
  "metrics": ["payments_count", "total_amount", "success_rate"]
}
```

**Ответ**:
```json
{
  "period": "week",
  "metrics": {
    "payments_count": 150,
    "total_amount": 1500000,
    "success_rate": 0.95
  },
  "chart_data": [
    {"date": "2024-01-15", "payments": 25, "amount": 250000},
    {"date": "2024-01-16", "payments": 30, "amount": 300000}
  ]
}
```

### 🏗️ Проекты

#### `create_project`
Создание нового проекта (требует авторизацию)

**Параметры**:
```json
{
  "name": "My AI Project",
  "description": "Project description",
  "settings": {
    "default_currency": "RUB",
    "webhook_url": "https://example.com/webhook"
  }
}
```

**Ответ**:
```json
{
  "project_id": 789,
  "name": "My AI Project",
  "api_key": "project_api_key",
  "created_at": "2024-01-15T10:00:00Z"
}
```

#### `create_project_anonymous`
**⭐ НОВОЕ:** Создание проекта без авторизации (для AI агентов)

**Параметры**:
```json
{
  "name": "My AI Project",
  "description": "Project description"
}
```

**Ответ**:
```json
{
  "project_id": 1025,
  "user_id": 1037,
  "user_api_key": "anon_ask_...",
  "project_api_key": "ask_...",
  "session_token": "...",
  "project": {
    "id": 1025,
    "name": "My AI Project"
  },
  "_auth_info": {
    "keys_received": {...},
    "ai_save_instructions": {...}
  }
}
```

**Особенности:**
- Автоматически создает анонимного пользователя с ANONYMOUS тарифом
- Возвращает `user_api_key` (префикс `anon_ask_`), `project_api_key`, `session_token`
- Пользователь сразу аутентифицирован и готов к использованию
- **⚠️ Ограничения ANONYMOUS тарифа:**
  - 1 проект максимум
  - 1 API ключ
  - 1,000 вызовов Logic Engine/месяц
  - 20 MB JSON хранилища
  - 20 триггеров
  - Платежи отключены
- Для конвертации в FREE тариф используйте `auth.convert_anonymous_user`

### ⏰ Планировщик

#### `schedule_task`
Планирование задачи

**Параметры**:
```json
{
  "project_id": 123,
  "name": "Daily Report",
  "schedule": "0 9 * * *",
  "endpoint": "https://api.example.com/reports",
  "method": "POST",
  "payload": {"type": "daily"}
}
```

**Ответ**:
```json
{
  "task_id": "task_456",
  "name": "Daily Report",
  "schedule": "0 9 * * *",
  "next_run": "2024-01-16T09:00:00Z",
  "status": "active"
}
```

### 🎁 Buff Management Tools (10 tools)

Система баффов позволяет применять временные или постоянные эффекты к пользователям и проектам. Идеально для триальных периодов, промо-акций, подписок и разовых покупок.

#### `buffs.create_buff`
Создание шаблона баффа в состоянии PENDING.

**Параметры**:
```json
{
  "entity_id": 1,
  "entity_kind": "user",
  "name": "7-Day Premium Trial",
  "type": "trial",
  "category": "subscription",
  "duration_days": 7,
  "priority": 100,
  "effects": {
    "data.limits.api_calls": 10000,
    "data.limits.logic_engine_calls": 5000
  },
  "config": {
    "revert_on_expire": true,
    "persistent": false
  }
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-string",
    "state": "pending",
    "created_at": "2025-01-20T10:00:00Z"
  }
}
```

#### `buffs.apply_buff`
Применение баффа к сущности (активация PENDING баффа).

**Параметры**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-here",
    "state": "active",
    "applied_at": "2025-01-20T10:00:00Z",
    "expires_at": "2025-01-27T10:00:00Z"
  }
}
```

#### `buffs.extend_buff`
Продление активного баффа.

**Параметры**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "additional_days": 30
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-here",
    "expires_at": "2025-02-20T10:00:00Z",
    "extended": true
  }
}
```

#### `buffs.revert_buff`
Откат активного баффа с валидацией успешности.

**Параметры**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "reverted": true,
    "buff_id": "uuid-here"
  }
}
```

**Ошибки:**
- `Revert failed: insufficient resources` - Недостаточно ресурсов для отката
- `Revert failed: cannot restore state` - Невозможно восстановить состояние

#### `buffs.cancel_buff`
Принудительная отмена баффа в любом состоянии.

**Параметры**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user"
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "cancelled": true,
    "buff_id": "uuid-here",
    "deleted": true,
    "reverted": false
  }
}
```

**Важно:** Для ACTIVE баффов требуется admin/owner права. Сначала выполняется revert, затем удаление.

#### `buffs.get_buff`
Получение информации о конкретном баффе.

**Параметры**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-here",
    "name": "7-Day Premium Trial",
    "state": "active",
    "is_active": true,
    "expires_at": "2025-01-27T10:00:00Z",
    "effects": {
      "data.limits.api_calls": 10000
    },
    "config": {
      "persistent": false,
      "revert_on_expire": true
    }
  }
}
```

#### `buffs.list_active_buffs`
Список активных баффов для сущности.

**Параметры**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "category": "subscription",
  "project_id": 1
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "active_buffs": [
      {
        "buff_id": "uuid-here",
        "name": "Premium Trial",
        "state": "active",
        "expires_at": "2025-01-27T10:00:00Z"
      }
    ],
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```

#### `buffs.get_effective_limits`
Получение эффективных лимитов с учетом всех активных баффов.

**Параметры**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "effective_limits": {
      "api_calls": 10000,
      "logic_engine_calls": 5000
    },
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```

#### `buffs.apply_temporary_effect`
Быстрое применение временного эффекта (создает и применяет в одном шаге).

**Параметры**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "name": "Weekend Bonus",
  "duration_days": 2,
  "effects": {
    "data.limits.api_calls": 5000
  }
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-here",
    "state": "active",
    "applied_at": "2025-01-20T10:00:00Z",
    "expires_at": "2025-01-22T10:00:00Z"
  }
}
```

#### `buffs.apply_persistent_effect`
Быстрое применение постоянного эффекта (создает и применяет в одном шаге).

**Параметры**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "name": "Lifetime Premium",
  "effects": {
    "data.features.premium": true,
    "data.limits.api_calls": 1000000
  }
}
```

**Ответ**:
```json
{
  "success": true,
  "data": {
    "buff_id": "uuid-here",
    "state": "active",
    "applied_at": "2025-01-20T10:00:00Z",
    "persistent": true
  }
}
```

### Workflows для баффов

**Workflow 1: Полный цикл триального периода**
```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 1,
      "entity_kind": "user",
      "name": "Trial",
      "duration_days": 7,
      "effects": {"data.limits.api_calls": 1000}
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<from previous step>",
      "entity_id": 1,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.list_active_buffs",
    "params": {
      "entity_id": 1,
      "entity_kind": "user"
    }
  }
]
```

**Workflow 2: Продление подписки**
```json
[
  {
    "tool": "buffs.list_active_buffs",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "category": "subscription"
    }
  },
  {
    "tool": "buffs.extend_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "additional_days": 30
    }
  }
]
```

## 🔍 Discovery API

### Получение списка инструментов
```bash
curl -H "X-API-Key: your_api_key" https://agentstack.tech/mcp/tools
```

**Ответ**:
```json
{
  "tools": [
    {
      "name": "create_payment",
      "description": "Create a new payment",
      "parameters": {
        "type": "object",
        "properties": {
          "merchant_id": {"type": "integer"},
          "amount_minor": {"type": "integer"},
          "currency": {"type": "string"}
        },
        "required": ["merchant_id", "amount_minor", "currency"]
      }
    }
  ]
}
```

### Информация об инструменте
```bash
curl -H "X-API-Key: your_api_key" https://agentstack.tech/mcp/tools/create_payment
```

## 🌊 Streaming API

### Streaming выполнение
```bash
curl -N -H "X-API-Key: your_api_key" \
     -H "Content-Type: application/json" \
     -d '{"merchant_id": 123, "amount_minor": 10000, "currency": "RUB"}' \
     https://agentstack.tech/mcp/tools/create_payment/stream
```

**Ответ** (Server-Sent Events):
```
data: {"status": "processing", "message": "Creating payment..."}

data: {"status": "progress", "progress": 50, "message": "Validating payment data..."}

data: {"status": "completed", "result": {"payment_id": "pay_123", "status": "pending"}}
```

## 🤖 Практические примеры - Что может делать AI агент?

AI агенты могут использовать MCP инструменты AgentStack для автоматизации различных задач. Вот практические примеры:

### Автоматизировать обработку платежей

**Задача:** Настроить биллинг, подписки, обработку транзакций

**Как это работает:**
- AI агент использует MCP tools для платежей (`create_payment`, `get_payment_status`, `pay_card`, `pay_usdt`)
- Настройка биллинга и подписок через MCP tools для биллинга
- Автоматическая обработка транзакций

**Пример запроса в Cursor/Claude:**
```
Настрой биллинг для проекта через MCP. Создай подписку на $29/месяц.
```

### Отправлять отчёты

**Задача:** Генерация и отправка аналитических отчётов

**Как это работает:**
- AI агент использует MCP tools для аналитики (`get_analytics`)
- Генерация отчётов через Logic Engine
- Отправка через webhooks или email через MCP tools для уведомлений

**Пример запроса в Cursor/Claude:**
```
Создай отчёт по проекту за последний месяц и отправь на email admin@example.com
```

### Управлять пользователями

**Задача:** Создание, обновление, управление правами доступа

**Как это работает:**
- AI агент использует MCP tools для проектов (`create_project`, `get_project`, `update_project`)
- Управление пользователями проектов через MCP tools
- Настройка RBAC через MCP tools для RBAC

**Пример запроса в Cursor/Claude:**
```
Добавь пользователя user@example.com в проект с ролью editor через MCP
```

### Применять баффы и эффекты

**Что такое бафы?** Бафы (Buffs) - это система временных и постоянных эффектов, которые изменяют лимиты, функции и ресурсы пользователей и проектов. 

**Временные эффекты (автоматически истекают и откатываются):**
- Триальные периоды (7-30 дней) - бесплатные пробные версии функций
- Промо-акции и события - ограниченные по времени предложения
- Временные бонусы - дополнительные ресурсы на короткий срок

**Постоянные эффекты (не истекают, требуют ручного управления):**
- Подписки - ежемесячные/годовые планы с возможностью продления
- Разовые покупки - одноразовые улучшения (например, увеличение лимитов)
- Пакеты улучшений - накопительные постоянные бонусы

**Задача:** Управление временными и постоянными эффектами для пользователей и проектов

**Как это работает:**
- AI агент использует MCP tools для баффов (`buffs.create_buff`, `buffs.apply_buff`, `buffs.extend_buff`)
- Создание триальных периодов через `buffs.apply_temporary_effect`
- Применение постоянных улучшений через `buffs.apply_persistent_effect`
- Управление подписками через продление баффов
- Интеграция с платежами для продажи подписок и покупок
- Автоматизация через Logic Engine и Scheduler

**Пример запроса в Cursor/Claude:**
```
Создай 7-дневный триал для пользователя с увеличенными лимитами API вызовов
```

**Пример запроса:**
```
Примени промо-бафф "Black Friday" к проекту с 50% скидкой на 30 дней
```

**Пример комплексного проекта:**
```
Создай SaaS платформу с автоматическими триалами для новых пользователей, подписками с авто-продлением и аналитикой использования
```

**Подробные примеры комплексных проектов:** См. [mcp_complex_projects.md](../examples/mcp_complex_projects.md), [mcp_buffs_workflows.md](../examples/mcp_buffs_workflows.md) и др. в [examples/](../examples/).

### Настраивать мониторинг

**Задача:** Настройка триггеров, webhooks, планировщика задач

**Как это работает:**
- AI агент использует MCP tools для Logic Engine (`create_logic_rule`, `update_logic_rule`)
- Настройка webhooks через MCP tools для webhooks
- Планировщик задач через MCP tools для Scheduler (`schedule_task`)

**Пример запроса в Cursor/Claude:**
```
Настрой webhook для события payment_completed через MCP. Отправляй уведомление на https://example.com/webhook
```

### Создавать проекты

**Задача:** Автоматическое создание и настройка проектов

**Как это работает:**
- AI агент использует MCP tools для проектов (`create_project` или `create_project_anonymous`)
- Автоматическая настройка проекта через MCP tools
- Получение API ключей автоматически

**Пример запроса в Cursor/Claude:**
```
Создай новый проект "My SaaS" через MCP с настройками для SaaS продукта
```

### Получать аналитику

**Задача:** Получение метрик и аналитики через чат

**Как это работает:**
- AI агент использует MCP tools для аналитики (`get_analytics`)
- Получение метрик проекта в реальном времени
- Визуализация данных через MCP tools

**Пример запроса в Cursor/Claude:**
```
Покажи статистику проекта за последний месяц: количество пользователей, платежи, активность
```

## 🔗 Интеграция с AI агентами

### Claude Desktop

Добавьте в конфигурацию Claude Desktop:

```json
{
  "mcpServers": {
    "agentstack": {
      "command": "npx",
      "args": ["@agentstack/mcp-client"],
      "env": {
        "MCP_SERVER_URL": "https://agentstack.tech/mcp",
        "MCP_API_KEY": "your_api_key"
      }
    }
  }
}
```

### Custom AI Agent

```python
import asyncio
import aiohttp

class AgentStackMCP:
    def __init__(self, api_key, base_url="https://agentstack.tech/mcp"):
        self.api_key = api_key
        self.base_url = base_url
        self.headers = {"X-API-Key": api_key}
    
    async def list_tools(self):
        async with aiohttp.ClientSession() as session:
            async with session.get(f"{self.base_url}/tools", headers=self.headers) as response:
                return await response.json()
    
    async def execute_tool(self, tool_name, parameters):
        async with aiohttp.ClientSession() as session:
            async with session.post(
                f"{self.base_url}/tools/{tool_name}",
                headers=self.headers,
                json=parameters
            ) as response:
                return await response.json()

# Использование
mcp = AgentStackMCP("your_api_key")

# Получение списка инструментов
tools = await mcp.list_tools()

# Создание платежа
payment = await mcp.execute_tool("create_payment", {
    "merchant_id": 123,
    "amount_minor": 10000,
    "currency": "RUB"
})
```

### JavaScript/Node.js

```javascript
class AgentStackMCP {
  constructor(apiKey, baseUrl = 'https://agentstack.tech/mcp') {
    this.apiKey = apiKey;
    this.baseUrl = baseUrl;
    this.headers = { 'X-API-Key': apiKey };
  }

  async listTools() {
    const response = await fetch(`${this.baseUrl}/tools`, {
      headers: this.headers
    });
    return response.json();
  }

  async executeTool(toolName, parameters) {
    const response = await fetch(`${this.baseUrl}/tools/${toolName}`, {
      method: 'POST',
      headers: {
        ...this.headers,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(parameters)
    });
    return response.json();
  }
}

// Использование
const mcp = new AgentStackMCP('your_api_key');

// Получение списка инструментов
const tools = await mcp.listTools();

// Создание платежа
const payment = await mcp.executeTool('create_payment', {
  merchant_id: 123,
  amount_minor: 10000,
  currency: 'RUB'
});
```

## 🔒 Безопасность

### API Key Management
- API ключи имеют ограниченные права доступа
- Поддержка scopes для контроля доступа к инструментам
- Rate limiting для предотвращения злоупотреблений
- Audit logging всех операций

### Валидация параметров
- Все параметры валидируются по схеме
- Sanitization входных данных
- Защита от injection атак

### Мониторинг
- Логирование всех MCP операций
- Метрики использования инструментов
- Алерты при подозрительной активности

## 📊 Мониторинг и метрики

### Метрики MCP
- Количество вызовов инструментов
- Время выполнения операций
- Частота ошибок
- Использование по API ключам

### Логирование
```json
{
  "timestamp": "2024-01-15T10:00:00Z",
  "level": "INFO",
  "tool": "create_payment",
  "api_key": "key_123",
  "project_id": 456,
  "duration_ms": 150,
  "status": "success"
}
```

## 🧪 Тестирование

### Тестовые API ключи
```bash
# Тестовый ключ для разработки
MCP_TEST_API_KEY="test_key_123"

# Тестовые данные
TEST_MERCHANT_ID=1
TEST_PROJECT_ID=1
```

### Примеры тестов
```bash
# Тест создания платежа
curl -X POST https://agentstack.tech/mcp/tools/create_payment \
  -H "X-API-Key: test_key_123" \
  -H "Content-Type: application/json" \
  -d '{
    "merchant_id": 1,
    "amount_minor": 1000,
    "currency": "RUB",
    "description": "Test payment"
  }'

# Тест получения статуса
curl -H "X-API-Key: test_key_123" \
     https://agentstack.tech/mcp/tools/get_payment_status \
     -d '{"payment_id": "pay_test_123"}'
```

## 🚀 Production Deployment

### Рекомендации
- Используйте HTTPS в продакшне
- Настройте proper rate limiting
- Включите мониторинг и алерты
- Регулярно ротируйте API ключи
- Настройте backup и disaster recovery

### Конфигурация
```bash
# Production настройки
MCP_HOST=0.0.0.0
MCP_PORT=8100
MCP_SSL_CERT=/path/to/cert.pem
MCP_SSL_KEY=/path/to/key.pem
MCP_RATE_LIMIT=1000
MCP_LOG_LEVEL=INFO
```

---

**Последнее обновление**: $(date)
**Версия MCP**: 2.0.0
**Статус**: Production Ready
