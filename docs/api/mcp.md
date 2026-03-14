# MCP Documentation

## 🤖 Model Context Protocol for AI agents

AgentStack provides a full-featured MCP (Model Context Protocol) server for integration with AI agents. MCP lets AI agents interact with the platform securely via a standardized protocol.

## 🔧 Configuration

MCP is available in the cloud at **[agentstack.tech](https://agentstack.tech)**. No local server setup required.

### Base URL

```text
https://agentstack.tech/mcp
```

### Authentication
```bash
# API Key in header
curl -H "X-API-Key: your_mcp_api_key" https://agentstack.tech/mcp/tools
```

### Public endpoints (no X-API-Key)

The following endpoints **can be called without the X-API-Key header** and are for onboarding and getting a first key:

| Method | Path | Description |
|--------|------|-------------|
| GET | `/mcp/tools` | List all MCP tools (descriptions and schemas). Used by scanners and clients before auth. |
| POST | `/mcp/tools` | JSON-RPC with `method: "tools/call"` and `params.name: "projects.create_project_anonymous"` — create anonymous project without a key. Returns `user_api_key`, `project_id`, etc. |
| POST | `/mcp/tools/projects.create_project_anonymous` | (Standalone MCP) Direct tool call; body `{"params": {"name": "..."}}`. Returns `api_key` / `user_api_key`, `project_id`. |

After getting a key from `projects.create_project_anonymous`, all other requests must be sent with header `X-API-Key: <user_api_key>`.

---

## 🧩 Execute (agentstack.execute)

MCP exposes one tool `agentstack.execute` that accepts **batched steps**. Base URL: `https://agentstack.tech/mcp`.

```http
POST https://agentstack.tech/mcp
X-API-Key: your_mcp_api_key
Content-Type: application/json

{
  "steps": [
    {
      "id": "p1",
      "action": "projects.create_project_anonymous",
      "params": { "name": "My project from MCP" }
    },
    {
      "id": "trial",
      "action": "buffs.apply_temporary_effect",
      "if": {
        "equals": [
          { "from": "p1.result.project_id" },
          { "from": "p1.result.project_id" }
        ]
      },
      "params": {
        "project_id": { "from": "p1.result.project_id" },
        "user_id": { "from": "context.user_id" },
        "effect": "trial_7_days"
      }
    }
  ],
  "options": { "stopOnError": true }
}
```

- `action` is the name of an MCP action, e.g. `projects.get_project`, `buffs.apply_temporary_effect`, `payments.create_payment` (full list via `GET /mcp/actions`).
- `params` is the parameter object for that tool.
- References to previous steps and context use `{ "from": "stepId.result.field" }` or `{ "from": "context.project_id" }`.
- Conditional execution uses the `if` field with simple JSON conditions (`equals`, `greater_than`, `and`, `or`, `exists`, etc.).

**Helper endpoints:**

| Method | Path | Description |
|--------|------|-------------|
| GET | `/mcp/actions` | List all available `action` values by domain (projects, buffs, auth, payments, logic, assets, scheduler, analytics, api_keys, rules, webhooks, notifications, wallets). |
| GET | `/mcp/discovery` | Discovery: protocol info and the single tool schema for `agentstack.execute`. |

The VS Code AgentStack plugin uses base URL `https://agentstack.tech/mcp` for Chat MCP and sidebar.

## 🛠️ Available tools

### 💳 Payment tools

#### `create_payment`
Create a new payment

**Parameters**:
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

**Response**:
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
Get payment status

**Parameters**:
```json
{
  "payment_id": "pay_123456"
}
```

**Response**:
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
Pay with card

**Parameters**:
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
Pay with USDT

**Parameters**:
```json
{
  "merchant_id": 123,
  "amount_minor": 10000,
  "currency": "USDT",
  "network": "TRC20",
  "wallet_address": "TWalletAddress"
}
```

### 🔐 Authentication

#### `quick_auth`
Quick user authentication

**Parameters**:
```json
{
  "user_id": 123,
  "project_id": 456,
  "permissions": ["payments:create", "analytics:read"]
}
```

**Response**:
```json
{
  "access_token": "jwt_token",
  "expires_in": 3600,
  "permissions": ["payments:create", "analytics:read"]
}
```

### 📊 Analytics

#### `get_analytics`
Get analytics data

**Parameters**:
```json
{
  "project_id": 123,
  "period": "week",
  "metrics": ["payments_count", "total_amount", "success_rate"]
}
```

**Response**:
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

### 🏗️ Projects

#### `create_project`
Create a new project (requires auth)

**Parameters**:
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

**Response**:
```json
{
  "project_id": 789,
  "name": "My AI Project",
  "api_key": "project_api_key",
  "created_at": "2024-01-15T10:00:00Z"
}
```

#### `create_project_anonymous`
**⭐ NEW:** Create a project without auth (for AI agents)

**Parameters**:
```json
{
  "name": "My AI Project",
  "description": "Project description"
}
```

**Response**:
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

**Details:**
- Automatically creates an anonymous user on ANONYMOUS tier
- Returns `user_api_key` (prefix `anon_ask_`), `project_api_key`, `session_token`
- User is immediately authenticated and ready to use
- **⚠️ ANONYMOUS tier limits:**
  - 1 project max
  - 1 API key
  - 1,000 Logic Engine calls/month
  - 20 MB JSON storage
  - 20 triggers
  - Payments disabled
- Use `auth.convert_anonymous_user` to convert to FREE tier

### ⏰ Scheduler

#### `schedule_task`
Schedule a task

**Parameters**:
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

**Response**:
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

The buff system lets you apply temporary or persistent effects to users and projects. Ideal for trials, promos, subscriptions, and one-time purchases.

#### `buffs.create_buff`
Create a buff template in PENDING state.

**Parameters**:
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

**Response**:
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
Apply a buff to an entity (activate a PENDING buff).

**Parameters**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Response**:
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
Extend an active buff.

**Parameters**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "additional_days": 30
}
```

**Response**:
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
Revert an active buff with success validation.

**Parameters**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "reverted": true,
    "buff_id": "uuid-here"
  }
}
```

**Errors:**
- `Revert failed: insufficient resources` — Insufficient resources to revert
- `Revert failed: cannot restore state` — Cannot restore state

#### `buffs.cancel_buff`
Force-cancel a buff in any state.

**Parameters**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user"
}
```

**Response**:
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

**Important:** For ACTIVE buffs admin/owner is required. Revert runs first, then deletion.

#### `buffs.get_buff`
Get info for a specific buff.

**Parameters**:
```json
{
  "buff_id": "uuid-here",
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Response**:
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
List active buffs for an entity.

**Parameters**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "category": "subscription",
  "project_id": 1
}
```

**Response**:
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
Get effective limits taking all active buffs into account.

**Parameters**:
```json
{
  "entity_id": 123,
  "entity_kind": "user",
  "project_id": 1
}
```

**Response**:
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
Quick-apply a temporary effect (create and apply in one step).

**Parameters**:
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

**Response**:
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
Quick-apply a persistent effect (create and apply in one step).

**Parameters**:
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

**Response**:
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

### Buff workflows

**Workflow 1: Full trial cycle**
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

**Workflow 2: Subscription renewal**
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

### Get tool list
```bash
curl -H "X-API-Key: your_api_key" https://agentstack.tech/mcp/tools
```

**Response**:
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

### Tool info
```bash
curl -H "X-API-Key: your_api_key" https://agentstack.tech/mcp/tools/create_payment
```

## 🌊 Streaming API

### Streaming execution
```bash
curl -N -H "X-API-Key: your_api_key" \
     -H "Content-Type: application/json" \
     -d '{"merchant_id": 123, "amount_minor": 10000, "currency": "RUB"}' \
     https://agentstack.tech/mcp/tools/create_payment/stream
```

**Response** (Server-Sent Events):
```
data: {"status": "processing", "message": "Creating payment..."}

data: {"status": "progress", "progress": 50, "message": "Validating payment data..."}

data: {"status": "completed", "result": {"payment_id": "pay_123", "status": "pending"}}
```

## 🤖 Practical examples — What can an AI agent do?

AI agents can use AgentStack MCP tools to automate various tasks. Here are practical examples:

### Automate payment handling

**Task:** Set up billing, subscriptions, transaction processing

**How it works:**
- AI agent uses MCP payment tools (`create_payment`, `get_payment_status`, `pay_card`, `pay_usdt`)
- Configure billing and subscriptions via MCP billing tools
- Automatic transaction processing

**Example Cursor/Claude request:**
```
Set up billing for the project via MCP. Create a $29/month subscription.
```

### Send reports

**Task:** Generate and send analytics reports

**How it works:**
- AI agent uses MCP analytics tools (`get_analytics`)
- Report generation via Logic Engine
- Send via webhooks or email using MCP notification tools

**Example Cursor/Claude request:**
```
Create a project report for the last month and send it to admin@example.com
```

### Manage users

**Task:** Create, update, manage access

**How it works:**
- AI agent uses MCP project tools (`create_project`, `get_project`, `update_project`)
- Project user management via MCP tools
- RBAC setup via MCP RBAC tools

**Example Cursor/Claude request:**
```
Add user user@example.com to the project with role editor via MCP
```

### Apply buffs and effects

**What are buffs?** Buffs are a system of temporary and persistent effects that change limits, features, and resources for users and projects.

**Temporary effects (expire and revert automatically):**
- Trial periods (7-30 days) — free trials of features
- Promos and events — time-limited offers
- Temporary bonuses — extra resources for a short time

**Persistent effects (do not expire, require manual management):**
- Subscriptions — monthly/yearly plans with renewal
- One-time purchases — single upgrades (e.g. higher limits)
- Upgrade packs — cumulative persistent bonuses

**Task:** Manage temporary and persistent effects for users and projects

**How it works:**
- AI agent uses MCP buff tools (`buffs.create_buff`, `buffs.apply_buff`, `buffs.extend_buff`)
- Create trials via `buffs.apply_temporary_effect`
- Apply persistent upgrades via `buffs.apply_persistent_effect`
- Manage subscriptions by extending buffs
- Integrate with payments to sell subscriptions and purchases
- Automate via Logic Engine and Scheduler

**Example Cursor/Claude request:**
```
Create a 7-day trial for the user with increased API call limits
```

**Example request:**
```
Apply "Black Friday" promo buff to the project with 50% discount for 30 days
```

**Example complex project:**
```
Create a SaaS platform with automatic trials for new users, auto-renew subscriptions, and usage analytics
```

**Detailed complex project examples:** See [mcp_complex_projects.md](../examples/mcp_complex_projects.md), [mcp_buffs_workflows.md](../examples/mcp_buffs_workflows.md), etc. in [examples/](../examples/).

### Set up monitoring

**Task:** Configure triggers, webhooks, task scheduler

**How it works:**
- AI agent uses MCP Logic Engine tools (`create_logic_rule`, `update_logic_rule`)
- Configure webhooks via MCP webhook tools
- Task scheduler via MCP Scheduler tools (`schedule_task`)

**Example Cursor/Claude request:**
```
Set up a webhook for payment_completed via MCP. Send notifications to https://example.com/webhook
```

### Create projects

**Task:** Automatically create and configure projects

**How it works:**
- AI agent uses MCP project tools (`create_project` or `create_project_anonymous`)
- Automatic project setup via MCP tools
- Get API keys automatically

**Example Cursor/Claude request:**
```
Create a new project "My SaaS" via MCP with settings for a SaaS product
```

### Get analytics

**Task:** Get metrics and analytics via chat

**How it works:**
- AI agent uses MCP analytics tools (`get_analytics`)
- Real-time project metrics
- Data visualization via MCP tools

**Example Cursor/Claude request:**
```
Show project stats for the last month: user count, payments, activity
```

## 🔗 Integration with AI agents

### Claude Desktop

Add to your Claude Desktop config:

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

# Usage
mcp = AgentStackMCP("your_api_key")

# Get tool list
tools = await mcp.list_tools()

# Create payment
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

// Usage
const mcp = new AgentStackMCP('your_api_key');

// Get tool list
const tools = await mcp.listTools();

// Create payment
const payment = await mcp.executeTool('create_payment', {
  merchant_id: 123,
  amount_minor: 10000,
  currency: 'RUB'
});
```

## 🔒 Security

### API Key Management
- API keys have limited access
- Scopes for controlling access to tools
- Rate limiting to prevent abuse
- Audit logging of all operations

### Parameter validation
- All parameters validated against schema
- Input sanitization
- Protection against injection attacks

### Monitoring
- Logging of all MCP operations
- Tool usage metrics
- Alerts on suspicious activity

## 📊 Monitoring and metrics

### MCP metrics
- Tool call count
- Operation duration
- Error rate
- Usage by API key

### Logging
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

## 🧪 Testing

### Test API keys
```bash
# Test key for development
MCP_TEST_API_KEY="test_key_123"

# Test data
TEST_MERCHANT_ID=1
TEST_PROJECT_ID=1
```

### Test examples
```bash
# Test payment creation
curl -X POST https://agentstack.tech/mcp/tools/create_payment \
  -H "X-API-Key: test_key_123" \
  -H "Content-Type: application/json" \
  -d '{
    "merchant_id": 1,
    "amount_minor": 1000,
    "currency": "RUB",
    "description": "Test payment"
  }'

# Test get status
curl -H "X-API-Key: test_key_123" \
     https://agentstack.tech/mcp/tools/get_payment_status \
     -d '{"payment_id": "pay_test_123"}'
```
