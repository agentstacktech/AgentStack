# MCP — Tools reference

Full list of MCP tools by category. Quick start: [MCP_QUICKSTART.md](MCP_QUICKSTART.md). Overview and API: [MCP_OVERVIEW.md](MCP_OVERVIEW.md).

---

## Available tools

### 🔐 Auth Tools (4 tools)

#### `auth.quick_auth`
Quick user authentication.

**Parameters:**
- `email` (string) - User email
- `password` (string) - Password

**Returns:** Access token and user info

#### `auth.create_user`
Create a new user.

**Parameters:**
- `email` (string)
- `password` (string)
- `display_name` (string, optional)

#### `auth.assign_role`
Assign a role to a user.

**Parameters:**
- `user_id` (int)
- `role` (string)
- `project_id` (int, optional)

#### `auth.get_profile`
Get user profile.

#### `auth.update_profile`
Update user profile.

---

### ⚙️ Logic Engine Tools (9 tools)

#### `logic.create`
Create a logic rule.

#### `logic.update`
Update a logic rule.

#### `logic.delete`
Delete a logic rule.

#### `logic.get`
Get rule info.

#### `logic.list`
List all project rules.

#### `logic.execute`
Execute a logic rule.

#### `logic.get_processors`
Get list of available processors.

#### `logic.get_commands`
Get list of available commands.

#### `logic.flush_batch`
Flush the rules batch queue.

---

### 💳 Payments Tools (4 tools)

#### `payments.create_payment`
Create a payment.

**Parameters:**
- `amount` (float)
- `currency` (string)
- `payment_method` (string)
- `description` (string, optional)

#### `payments.get_status`
Get payment status.

**Parameters:**
- `payment_id` (string)

#### `payments.refund`
Refund a payment.

**Parameters:**
- `payment_id` (string)
- `amount` (float, optional) - partial refund

#### `payments.list_transactions`
List transactions.

**Parameters:**
- `limit` (int, optional)
- `offset` (int, optional)

#### `payments.get_balance`
Get balance.

---

### 📁 Projects Tools (8 tools)

#### Core CRUD

##### `projects.get_projects`
Get list of projects.

**Parameters:**
- `is_active` (bool, optional)
- `limit` (int, optional)
- `offset` (int, optional)

##### `projects.get_project`
Get project by ID.

**Parameters:**
- `project_id` (int)

##### `projects.create_project`
Create a project (requires auth).

**Parameters:**
- `name` (string) - Required
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `preset_id` (string, optional)
- `settings` (dict, optional)

##### `projects.create_project_anonymous`
**⭐ NEW:** Create a project without auth (for AI agents).

**Parameters:**
- `name` (string) - Required
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `preset_id` (string, optional)
- `settings` (dict, optional)

**Returns:**
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

**Details:**
- No auth required
- **Creates an anonymous user** with `is_anonymous: true`
- Generates API key with prefix `anon_ask_` (ANONYMOUS tier)
- Returns `user_api_key`, `project_api_key`, `session_token`, `user_id`
- User is immediately authenticated and ready to use

**⚠️ ANONYMOUS tier limits:**
- **1 project max** - Cannot create more projects
- **1 API key** - Cannot create more keys
- **1,000 Logic Engine calls/month** (vs 10,000 on FREE)
- **20 MB JSON storage** (vs 100 MB on FREE)
- **20 triggers** (vs 50 on FREE)
- **Payments disabled** - Cannot process payments

**Convert to full account:**
- Use `auth.convert_anonymous_user` to convert to FREE tier
- After conversion all limits increase to FREE tier
- User can sign in with email/password
- Can create additional projects and API keys

##### `projects.update_project`
Update a project.

**Parameters:**
- `project_id` (int)
- `name` (string, optional)
- `description` (string, optional)
- `config` (dict, optional)
- `data` (dict, optional)
- `is_active` (bool, optional)

##### `projects.delete_project`
Delete a project.

**Parameters:**
- `project_id` (int)

#### Stats and analytics

##### `projects.get_stats`
Get project stats.

**Parameters:**
- `project_id` (int)

**Returns:** Project stats (users, activity, usage)

#### User management

##### `projects.get_users`
Get list of project users.

**Parameters:**
- `project_id` (int)
- `is_active` (bool, optional)
- `role` (string, optional)
- `limit` (int, optional)
- `offset` (int, optional)

##### `projects.add_user`
**⭐ REQUIRES Professional subscription**

Add a user to the project.

**Parameters:**
- `project_id` (int)
- `user_id` (int, optional)
- `email` (string, optional)
- `role` (string) - Default "member"

**Limits:**
- Professional subscription required (pro, professional, enterprise)
- Check at API endpoint level
- 403 when subscription is missing

##### `projects.remove_user`
**⭐ REQUIRES Professional subscription**

Remove a user from the project.

**Parameters:**
- `project_id` (int)
- `user_id` (int)

##### `projects.update_user_role`
Update a user's role in the project.

**Parameters:**
- `project_id` (int)
- `user_id` (int)
- `role` (string)

##### `projects.attach_to_user`
**⭐ NEW:** Attach an anonymous project to a user.

**Parameters:**
- `project_id` (int)
- `auth_key` (string) - Key from anonymous creation
- `user_id` (int, optional)
- `email` (string, optional)

**Process:**
1. Validates `auth_key` against project API keys
2. Updates `project.user_id` to the new owner
3. Creates records in `data_projects_user`
4. Removes old anonymous key
5. Creates new API key for the owner

**Returns:**
```json
{
  "success": true,
  "project_id": 1025,
  "new_api_key": "ask_...",
  "message": "Project successfully attached to user"
}
```

#### API key management

##### `projects.get_api_keys`
Get list of project API keys.

**Parameters:**
- `project_id` (int)

**Uses:** `GET /api/apikeys/keys?project_id={project_id}`

##### `projects.create_api_key`
Create an API key for the project.

**Parameters:**
- `project_id` (int)
- `name` (string)
- `scopes` (array, optional)
- `expires_at` (string, optional)

**Uses:** `POST /api/apikeys/keys` with `project_id` in body

##### `projects.delete_api_key`
Delete a project API key.

**Parameters:**
- `project_id` (int)
- `key_id` (string)

**Uses:** `DELETE /api/apikeys/keys/{key_id}?project_id={project_id}`

#### Project settings

##### `projects.get_settings`
Get project settings (reads `config`).

**Parameters:**
- `project_id` (int)

##### `projects.update_settings`
Update project settings (updates `config`).

**Parameters:**
- `project_id` (int)
- `settings` (dict)

#### Project activity

##### `projects.get_activity`
Get project activity (logs).

**Parameters:**
- `project_id` (int)
- `limit` (int, optional)
- `offset` (int, optional)

**Uses:** `GET /api/projects/{project_id}/logs`

---

### ⏰ Scheduler Tools (11 tools)

#### `scheduler.schedule_task`
Schedule a task.

**Parameters:**
- `project_id` (int)
- `run_at` (string) - ISO datetime
- `tool` (string)
- `payload` (dict)

#### `scheduler.cancel_task`
Cancel a task.

**Parameters:**
- `task_id` (string)

#### `scheduler.get_task`
Get task info.

**Parameters:**
- `task_id` (string)

#### `scheduler.list_tasks`
List tasks.

**Parameters:**
- `project_id` (int, optional)

#### `scheduler.update_task`
Update a task.

**Parameters:**
- `task_id` (string)
- `task_data` (dict)

---

### 📊 Analytics Tools (2 tools)

#### `analytics.get_usage`
Get usage stats.

**Parameters:**
- `project_id` (int, optional)
- `start_date` (string, optional)
- `end_date` (string, optional)

#### `analytics.set_budget`
Set budget.

**Parameters:**
- `project_id` (int)
- `budget` (float)
- `period` (string) - "monthly", "yearly"

#### `analytics.get_metrics`
Get metrics.

**Parameters:**
- `project_id` (int, optional)
- `metric_type` (string, optional)

#### `analytics.export_data`
Export analytics data.

**Parameters:**
- `project_id` (int)
- `format` (string) - "json", "csv"
- `start_date` (string, optional)
- `end_date` (string, optional)

#### `analytics.get_dashboard`
Get dashboard data.

**Parameters:**
- `project_id` (int, optional)

---

### 🔑 API Keys Tools (3 tools)

#### `api_keys.create_key`
Create an API key.

**Parameters:**
- `project_id` (int)
- `name` (string)
- `scopes` (array, optional)

#### `api_keys.revoke_key`
Revoke an API key.

**Parameters:**
- `key_id` (string)

#### `api_keys.list_keys`
List API keys.

**Parameters:**
- `project_id` (int, optional)

#### `api_keys.update_key`
Update an API key.

**Parameters:**
- `key_id` (string)
- `key_data` (dict)

#### `api_keys.get_usage`
Get API key usage.

**Parameters:**
- `key_id` (string)

---

### ⚙️ Rules Tools (6 tools)

#### `rules.create_rules`
Create rules.

**Parameters:**
- `project_id` (int)
- `json_rules` (dict)
- `name` (string, optional)
- `description` (string, optional)

#### `rules.test_rules`
Test rules.

**Parameters:**
- `rule_id` (string)
- `test_data` (dict)

#### `rules.activate_rules`
Activate rules.

**Parameters:**
- `rule_id` (string)

#### `rules.list_rules`
List rules.

**Parameters:**
- `project_id` (int, optional)

#### `rules.update_rules`
Update rules.

**Parameters:**
- `rule_id` (string)
- `rule_data` (dict)

#### `rules.delete_rules`
Delete rules.

**Parameters:**
- `rule_id` (string)

---

### 🔔 Webhooks Tools (5 tools)

#### `webhooks.create_endpoint`
Create a webhook endpoint.

**Parameters:**
- `project_id` (int)
- `url` (string)
- `events` (array)
- `secret` (string, optional)

#### `webhooks.list_endpoints`
List webhook endpoints.

**Parameters:**
- `project_id` (int, optional)

#### `webhooks.update_endpoint`
Update a webhook endpoint.

**Parameters:**
- `endpoint_id` (string)
- `endpoint_data` (dict)

#### `webhooks.delete_endpoint`
Delete a webhook endpoint.

**Parameters:**
- `endpoint_id` (string)

#### `webhooks.test_endpoint`
Test a webhook endpoint.

**Parameters:**
- `endpoint_id` (string)
- `test_data` (dict, optional)

---

### 📬 Notifications Tools (4 tools)

#### `notifications.send_notification`
Send a notification.

**Parameters:**
- `project_id` (int)
- `user_id` (int, optional)
- `type` (string)
- `message` (string)
- `data` (dict, optional)

#### `notifications.list_notifications`
List notifications.

**Parameters:**
- `project_id` (int)
- `user_id` (int, optional)
- `limit` (int, optional)

#### `notifications.create_template`
Create a notification template.

**Parameters:**
- `project_id` (int)
- `name` (string)
- `template` (string)
- `variables` (array, optional)

#### `notifications.update_template`
Update a notification template.

**Parameters:**
- `template_id` (string)
- `template_data` (dict)

---

### 💰 Wallets Tools (4 tools)

#### `wallets.get_balance`
Get wallet balance.

**Parameters:**
- `project_id` (int)
- `wallet_id` (int, optional)

#### `wallets.list_transactions`
List wallet transactions.

**Parameters:**
- `project_id` (int)
- `wallet_id` (int, optional)
- `limit` (int, optional)

#### `wallets.create_wallet`
Create a wallet.

**Parameters:**
- `project_id` (int)
- `name` (string)
- `type` (string) - "business", "personal"
- `currency` (string)

#### `wallets.update_wallet`
Update a wallet.

**Parameters:**
- `wallet_id` (int)
- `wallet_data` (dict)

---

### 🎁 Buff Management Tools (10 tools)

**What are buffs?** Buffs are a system of temporary and persistent effects that change limits, features, and resources for users and projects.

**Temporary effects (expire and revert automatically):**
- Trial periods (7-30 days) — free trials of features
- Promos and events — time-limited offers
- Temporary bonuses — extra resources for a short time

**Persistent effects (do not expire, require manual management):**
- Subscriptions — monthly/yearly plans with renewal
- One-time purchases — single upgrades (e.g. higher limits)
- Upgrade packs — cumulative persistent bonuses

The buff system is ideal for managing trials, promos, subscriptions, and one-time purchases in your SaaS, game, or app.

#### `buffs.create_buff`
Create a buff template in PENDING state.

**Parameters:**
- `entity_id` (int, required) - Entity ID (user or project)
- `entity_kind` (string, required) - Entity kind: "user" or "project"
- `name` (string, required) - Buff name
- `type` (string, required) - Buff type (trial, promo, subscription, purchase)
- `category` (string, required) - Category for filtering
- `duration_days` (int, optional, default: 30) - Duration in days
- `priority` (int, optional, default: 100) - Priority (0-3000)
- `effects` (object, optional) - Effects as {"data.limits.api_calls": 10000}
- `config` (object, optional) - Config (persistent, revert_on_expire, etc.)
- `project_id` (int, optional) - Project ID (for user buffs)

**Example:**
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
Apply a buff to an entity (activate a PENDING buff).

**Parameters:**
- `buff_id` (string, required) - Buff UUID
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `project_id` (int, optional) - Project ID

**Example:**
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
Extend an active buff.

**Parameters:**
- `buff_id` (string, required) - Buff UUID
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `additional_days` (int, optional) - Extra days (if omitted, extends by original duration)
- `project_id` (int, optional) - Project ID

**Example:**
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
Revert an active buff with success validation.

**Parameters:**
- `buff_id` (string, required) - Buff UUID
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `project_id` (int, optional) - Project ID

**Important:** Revert checks that resources can be restored. If the buff added 100 gold, revert must subtract 100 gold. If revert is impossible (insufficient resources), an error is thrown.

**Example:**
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
Force-cancel a buff in any state.

**Parameters:**
- `buff_id` (string, required) - Buff UUID
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `project_id` (int, optional) - Project ID

**Important:**
- For PENDING buffs — normal permissions
- For ACTIVE buffs — admin/owner required
- For ACTIVE buffs revert runs first, then deletion

**Example:**
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
Get info for a specific buff.

**Parameters:**
- `buff_id` (string, required) - Buff UUID
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `project_id` (int, optional) - Project ID

**Returns:** Full buff info including state, effects, config, expiry.

#### `buffs.list_active_buffs`
List active buffs for an entity.

**Parameters:**
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `category` (string, optional) - Filter by category
- `project_id` (int, optional) - Project ID
- `include_ecosystem` (bool, optional) - Include ecosystem buffs (for projects)

**Example:**
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
Get effective limits taking all active buffs into account.

**Parameters:**
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `project_id` (int, optional) - Project ID

**Returns:** Effective limits after applying all active buffs.

**Example:**
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
Quick-apply a temporary effect (create and apply in one step).

**Parameters:**
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `name` (string, required) - Effect name
- `duration_days` (int, required) - Duration in days
- `effects` (object, required) - Effects
- `priority` (int, optional, default: 100) - Priority
- `project_id` (int, optional) - Project ID

**Example:**
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
Quick-apply a persistent effect (create and apply in one step).

**Parameters:**
- `entity_id` (int, required) - Entity ID
- `entity_kind` (string, required) - Entity kind
- `name` (string, required) - Effect name
- `effects` (object, required) - Effects
- `priority` (int, optional, default: 300) - Priority
- `project_id` (int, optional) - Project ID

**Example:**
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

### Workflows and tool combinations

**Workflow 1: Full trial cycle**
```json
[
  {"tool": "buffs.create_buff", "params": {...}},
  {"tool": "buffs.apply_buff", "params": {"buff_id": "<from previous>"}},
  {"tool": "buffs.list_active_buffs", "params": {...}}
]
```

**Workflow 2: Subscription renewal**
```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.extend_buff", "params": {"buff_id": "<subscription_buff_id>"}},
  {"tool": "buffs.get_buff", "params": {"buff_id": "<subscription_buff_id>"}}
]
```

**Workflow 3: Cancel a wrong buff**
```json
[
  {"tool": "buffs.get_buff", "params": {...}},
  {"tool": "buffs.revert_buff", "params": {...}},
  {"tool": "buffs.cancel_buff", "params": {...}}
]
```

### Usage scenarios

**What are buffs?** Buffs are a system of temporary and persistent effects that change limits, features, and resources for users and projects. Ideal for managing trials, promos, subscriptions, and one-time purchases.

**Temporary effects (expire and revert automatically):**
- **Trial periods (7-30 days)** — free trials of features for new users
- **Promos and events** — time-limited offers (e.g. Black Friday)
- **Temporary bonuses** — extra resources for a short time (e.g. double XP on weekends)
- **Seasonal offers** — holiday and special events

**Persistent effects (do not expire, require manual management):**
- **Subscriptions (with renewal)** — monthly/yearly plans with optional auto-renewal
- **One-time purchases** — single upgrades (e.g. higher API call limits)
- **Upgrade packs** — cumulative persistent bonuses (can buy multiple times)
- **Lifetime upgrades** — permanent upgrades with no expiry

**Synergy with other systems:**
- **Buffs + Logic Engine** — auto-apply trials on user registration
- **Buffs + Payments** — sell subscriptions and one-time purchases via payments
- **Buffs + Scheduler** — auto-renew subscriptions on a schedule
- **Buffs + Analytics** — track promo effectiveness and trial conversion
- **Buffs + Notifications** — notify about new buffs, trial expiry, and promos

**Detailed complex project examples:** See [examples/mcp_complex_projects.md](examples/mcp_complex_projects.md), [examples/mcp_buffs_workflows.md](examples/mcp_buffs_workflows.md), [examples/mcp_buffs_temporary.md](examples/mcp_buffs_temporary.md), [examples/mcp_buffs_persistent.md](examples/mcp_buffs_persistent.md).

---