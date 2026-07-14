# MCP Project Setup Playbooks

**Audience:** AI agents and developers using `agentstack.execute` via MCP  
**Goal:** Mass-configure projects from scratch or migrate existing ones with reproducible, atomic steps.  
**Full business flows:** `docs/MCP_BUSINESS_FLOWS.md`

---

## Index

1. [Playbook 1 — Minimal Project Bootstrap](#1-minimal-project-bootstrap)
2. [Playbook 2 — Full Economy Setup](#2-full-economy-setup)
3. [Playbook 3 — RBAC + Team Setup](#3-rbac--team-setup)
4. [Playbook 4 — Logic Rules Battery](#4-logic-rules-battery)
5. [Playbook 5 — Scheduler Configuration](#5-scheduler-configuration)
6. [Playbook 6 — Field Access Policy (FAP)](#6-field-access-policy-fap)
7. [Playbook 7 — API Keys Provisioning](#7-api-keys-provisioning)
8. [Playbook 8 — RAG Collections Setup](#8-rag-collections-setup)
9. [Playbook 9 — Buff Templates Library](#9-buff-templates-library)
10. [Playbook 10 — Complete Production Bootstrap](#10-complete-production-bootstrap)
11. [Mass Operations Patterns](#11-mass-operations-patterns)

---

## How `agentstack.execute` batch works

All steps in one `POST /mcp` call run in sequence. Use `{"from": "stepId.result.field"}`
to pass values between steps. Use `"if"` to gate steps on prior results.

```json
POST /mcp
{
  "steps": [
    {"id": "step1", "action": "projects.create_project", "params": {...}},
    {"id": "step2", "action": "wallets.create", "params": {"project_id": {"from": "step1.result.project_id"}}}
  ]
}
```

---

## 1. Minimal Project Bootstrap

**Use when:** Starting any new project — anonymous signup or authenticated.

```json
{
  "steps": [
    {
      "id": "project",
      "action": "projects.create_project_anonymous",
      "params": {
        "name": "My App",
        "data": {"config": {"version": "1.0", "env": "production"}}
      }
    },
    {
      "id": "ping",
      "action": "system.ping",
      "params": {}
    }
  ]
}
```

**Save from response:**
- `project.result.project_id` — all subsequent calls need this
- `project.result.user_api_key` — authenticate this session
- `project.result.session_token` — JWT for dashboard use

> For authenticated users: use `projects.create_project` (requires existing session).

---

## 2. Full Economy Setup

**Use when:** Game economy, SaaS credits, loyalty program — anything with
currencies, wallets, and initial balances.

```json
{
  "steps": [
    {
      "id": "soft_currency",
      "action": "assets.create",
      "params": {
        "name": "Credits",
        "type": "currency",
        "data": {"symbol": "CR", "decimals": 0, "earnable": true, "max_wallet_balance": 100000}
      }
    },
    {
      "id": "hard_currency",
      "action": "assets.create",
      "params": {
        "name": "Gems",
        "type": "currency",
        "data": {"symbol": "GEM", "decimals": 0, "purchasable": true, "premium": true}
      }
    },
    {
      "id": "xp_asset",
      "action": "assets.create",
      "params": {
        "name": "Experience Points",
        "type": "points",
        "data": {"symbol": "XP", "non_transferable": true}
      }
    },
    {
      "id": "main_wallet",
      "action": "wallets.create",
      "params": {"name": "Main Credits Wallet", "currency": "credits"}
    },
    {
      "id": "gem_wallet",
      "action": "wallets.create",
      "params": {"name": "Gems Wallet", "currency": "gems"}
    },
    {
      "id": "seed_credits",
      "action": "wallets.deposit",
      "params": {
        "wallet_id": {"from": "main_wallet.result.wallet_id"},
        "amount": 500,
        "description": "Initial credits pool"
      }
    },
    {
      "id": "check_balance",
      "action": "payments.get_balance",
      "params": {"wallet_id": {"from": "main_wallet.result.wallet_id"}}
    }
  ]
}
```

**Multi-project economy bridge** (cross-project exchange via REST after setup):
```
POST /api/exchange/quote  →  POST /api/exchange/execute
```
See `docs/MCP_ECONOMY_AND_COMMERCE_MAP.md`.

---

## 3. RBAC + Team Setup

**Use when:** Onboarding a team — invite members, assign roles, verify permissions.

```json
{
  "steps": [
    {
      "id": "list_roles",
      "action": "rbac.get_roles",
      "params": {}
    },
    {
      "id": "add_admin",
      "action": "projects.add_user",
      "params": {"user_id": "$admin_user_id", "role": "admin"}
    },
    {
      "id": "assign_admin_role",
      "action": "rbac.assign_role",
      "params": {"user_id": "$admin_user_id", "role": "admin"}
    },
    {
      "id": "add_member_1",
      "action": "projects.add_user",
      "params": {"user_id": "$member_1_user_id", "role": "member"}
    },
    {
      "id": "add_member_2",
      "action": "projects.add_user",
      "params": {"user_id": "$member_2_user_id", "role": "member"}
    },
    {
      "id": "verify_admin",
      "action": "rbac.check_permission",
      "params": {"user_id": "$admin_user_id", "permission": "write"}
    },
    {
      "id": "list_team",
      "action": "projects.get_users",
      "params": {}
    }
  ]
}
```

**Bulk role migration** (when changing all members from `member` to `contributor`):
```json
{
  "steps": [
    {
      "id": "get_users",
      "action": "projects.get_users",
      "params": {}
    },
    {
      "id": "update_role_batch",
      "action": "commands.execute_batch",
      "params": {
        "commands": "$member_ids_array",
        "template": {
          "command_type": "rbac",
          "command_name": "update_role",
          "payload": {"user_id": "$item", "role": "contributor"}
        }
      }
    }
  ]
}
```

---

## 4. Logic Rules Battery

**Use when:** Wiring up business logic for a vertical — set up all rules in one batch.

### Game logic battery
```json
{
  "steps": [
    {
      "id": "get_processors",
      "action": "logic.get_processors",
      "params": {}
    },
    {
      "id": "xp_on_action",
      "action": "logic.create",
      "params": {
        "name": "XP on any action",
        "triggers": [{"type": "command", "command": "game.action_completed"}],
        "space": [{"processor": "field_operations_processor", "action": "add",
          "parameters": {"field1": "user.data.xp", "field2": 5, "result_field": "user.data.xp"}}],
        "enabled": true
      }
    },
    {
      "id": "level_up_rule",
      "action": "logic.create",
      "params": {
        "name": "Level up at 100 XP",
        "triggers": [{"type": "field_change", "field": "user.data.xp"}],
        "conditions": [{"field": "user.data.xp", "op": "gte", "value": 100}],
        "space": [
          {"processor": "field_operations_processor", "action": "add",
            "parameters": {"field1": "user.data.level", "field2": 1, "result_field": "user.data.level"}},
          {"processor": "field_operations_processor", "action": "set",
            "parameters": {"field": "user.data.xp", "value": 0}}
        ],
        "enabled": true
      }
    },
    {
      "id": "daily_streak_rule",
      "action": "logic.create",
      "params": {
        "name": "Daily streak tracker",
        "triggers": [{"type": "command", "command": "game.daily_login"}],
        "space": [
          {"processor": "field_operations_processor", "action": "add",
            "parameters": {"field1": "user.data.streak", "field2": 1, "result_field": "user.data.streak"}}
        ],
        "enabled": true
      }
    },
    {
      "id": "anti_cheat_rule",
      "action": "logic.create",
      "params": {
        "name": "Anti-cheat: cap XP gain per session",
        "triggers": [{"type": "field_change", "field": "user.data.session_xp"}],
        "conditions": [{"field": "user.data.session_xp", "op": "gt", "value": 500}],
        "space": [
          {"processor": "field_operations_processor", "action": "set",
            "parameters": {"field": "user.data.session_xp", "value": 500}},
          {"processor": "field_operations_processor", "action": "set",
            "parameters": {"field": "user.data.suspicious_activity", "value": true}}
        ],
        "enabled": true
      }
    }
  ]
}
```

### SaaS logic battery
```json
{
  "steps": [
    {
      "id": "api_call_counter",
      "action": "logic.create",
      "params": {
        "name": "Count API calls",
        "triggers": [{"type": "command", "command": "system.api_call_authenticated"}],
        "space": [{"processor": "field_operations_processor", "action": "add",
          "parameters": {"field1": "user.data.api_calls_month", "field2": 1, "result_field": "user.data.api_calls_month"}}],
        "enabled": true
      }
    },
    {
      "id": "quota_exceeded_rule",
      "action": "logic.create",
      "params": {
        "name": "Quota exceeded alert",
        "triggers": [{"type": "field_change", "field": "user.data.api_calls_month"}],
        "conditions": [{"field": "user.data.api_calls_month", "op": "gte", "value": "$quota_limit"}],
        "space": [{"processor": "field_operations_processor", "action": "set",
          "parameters": {"field": "user.data.quota_exceeded", "value": true}}],
        "enabled": true
      }
    },
    {
      "id": "feature_flag_rule",
      "action": "logic.create",
      "params": {
        "name": "Feature flag: advanced analytics",
        "triggers": [{"type": "command", "command": "feature.advanced_analytics_requested"}],
        "conditions": [{"field": "user.data.plan", "op": "in", "value": ["pro", "enterprise"]}],
        "space": [{"processor": "field_operations_processor", "action": "set",
          "parameters": {"field": "user.data.advanced_analytics_allowed", "value": true}}],
        "enabled": true
      }
    }
  ]
}
```

**Tips:**
- Always call `logic.get_processors` first to discover available processor names
- Call `logic.get_commands` to see available trigger command names
- `conditions` array = AND logic; for OR, create separate rules

---

## 5. Scheduler Configuration

**Use when:** Setting up all recurring tasks for a project — maintenance, billing, reports, cleanup.

```json
{
  "steps": [
    {
      "id": "daily_cleanup",
      "action": "scheduler.create_task",
      "params": {
        "name": "Daily: cleanup expired sessions",
        "task_type": "cron",
        "cron_expression": "0 4 * * *",
        "payload": {"action": "cleanup_expired_sessions"}
      }
    },
    {
      "id": "weekly_report",
      "action": "scheduler.create_task",
      "params": {
        "name": "Weekly: usage report",
        "task_type": "cron",
        "cron_expression": "0 9 * * 1",
        "payload": {"action": "generate_weekly_report", "send_email": true}
      }
    },
    {
      "id": "monthly_billing",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly: process billing",
        "task_type": "cron",
        "cron_expression": "0 10 1 * *",
        "payload": {"action": "process_monthly_billing"}
      }
    },
    {
      "id": "monthly_reset",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly: reset counters",
        "task_type": "cron",
        "cron_expression": "0 0 1 * *",
        "payload": {"action": "reset_monthly_counters", "fields": ["api_calls_month", "articles_read"]}
      }
    },
    {
      "id": "health_check",
      "action": "scheduler.create_task",
      "params": {
        "name": "Hourly: health check",
        "task_type": "cron",
        "cron_expression": "0 * * * *",
        "payload": {"action": "system_health_check"}
      }
    },
    {
      "id": "verify_tasks",
      "action": "scheduler.list_tasks",
      "params": {}
    }
  ]
}
```

**Cron quick-reference:**

| Pattern              | Meaning                     |
|----------------------|-----------------------------|
| `0 4 * * *`          | Every day at 04:00          |
| `0 9 * * 1`          | Every Monday at 09:00       |
| `0 0 1 * *`          | 1st of every month 00:00    |
| `0 10 1 * *`         | 1st of every month 10:00    |
| `0 * * * *`          | Every hour                  |
| `*/15 * * * *`       | Every 15 minutes            |
| `0 0 * * 0`          | Every Sunday midnight       |

---

## 6. Field Access Policy (FAP)

**Use when:** Protecting sensitive user/project data — hide fields from unauthorized
roles, or gate content behind subscription tiers.

```json
{
  "steps": [
    {
      "id": "apply_global_template",
      "action": "data_access.apply_defaults_template",
      "params": {}
    },
    {
      "id": "set_user_policy",
      "action": "data_access.set_policy",
      "params": {
        "resources": {
          "user_profile": {
            "default_access": "allow",
            "fields": {
              "email": {"rule": "owner_only"},
              "payment_method": {"rule": "deny"},
              "internal_score": {"rule": "admin_only"}
            }
          },
          "premium_content": {
            "default_access": "deny",
            "fields": {
              "preview": {"rule": "allow"},
              "body": {
                "rule": "allow_if",
                "condition": {"field": "user.data.plan", "op": "in", "value": ["pro", "enterprise"]}
              }
            }
          },
          "financial_data": {
            "default_access": "deny",
            "fields": {
              "balance": {"rule": "owner_only"},
              "transactions": {
                "rule": "allow_if",
                "condition": {"field": "user.data.verified", "op": "equals", "value": true}
              }
            }
          }
        }
      }
    },
    {
      "id": "verify_policy",
      "action": "data_access.get_policy",
      "params": {}
    },
    {
      "id": "test_mask",
      "action": "data_access.test_mask",
      "params": {
        "resource": "user_profile",
        "user_role": "member",
        "sample_data": {"email": "test@test.com", "internal_score": 99}
      }
    }
  ]
}
```

**FAP quick-reference:**

| Rule            | Meaning                                        |
|-----------------|------------------------------------------------|
| `"allow"`       | Everyone can see this field                    |
| `"deny"`        | Nobody sees this field                         |
| `"owner_only"`  | Only the resource owner can see it             |
| `"admin_only"`  | Only admin+ roles can see it                   |
| `"allow_if"`    | Conditional — specify `condition` object       |

---

## 7. API Keys Provisioning

**Use when:** Creating multiple restricted keys for different agents/services/environments.

```json
{
  "steps": [
    {
      "id": "admin_key",
      "action": "apikeys.create",
      "params": {
        "name": "Admin Full Access Key",
        "service_caps": null
      }
    },
    {
      "id": "payment_agent_key",
      "action": "apikeys.create",
      "params": {
        "name": "Payment Agent (payments only)",
        "service_caps": ["payments"]
      }
    },
    {
      "id": "scheduler_key",
      "action": "apikeys.create",
      "params": {
        "name": "Scheduler Bot (tasks only)",
        "service_caps": ["scheduler"]
      }
    },
    {
      "id": "analytics_key",
      "action": "apikeys.create",
      "params": {
        "name": "Analytics Dashboard (read-only)",
        "service_caps": ["analytics"]
      }
    },
    {
      "id": "game_loop_key",
      "action": "apikeys.create",
      "params": {
        "name": "Game Loop Agent (pay + schedule)",
        "service_caps": ["payments", "scheduler"]
      }
    },
    {
      "id": "readonly_key",
      "action": "apikeys.create",
      "params": {
        "name": "Observer (no cap-gated services)",
        "service_caps": []
      }
    },
    {
      "id": "list_keys",
      "action": "apikeys.list",
      "params": {}
    }
  ]
}
```

**Store the returned `api_key` values immediately** — they are shown only once.

**service_caps reference:**

| Cap             | Protects                          | MCP tools blocked without cap |
|-----------------|-----------------------------------|-------------------------------|
| `"payments"`    | Payments + wallets                | `payments.*`, `wallets.*`     |
| `"scheduler"`   | Task scheduling                   | `scheduler.*`                 |
| `"analytics"`   | Analytics REST endpoints          | REST `/api/analytics/*`       |
| `[]` (empty)    | All cap-gated services blocked    | All of the above              |
| `null` (omit)   | No restriction (full access)      | None                          |

Full guide: `docs/API_KEY_SERVICE_CAPS.md`

---

## 8. RAG Collections Setup

**Use when:** Building semantic search, AI memory, knowledge base, or content indexing.

```json
{
  "steps": [
    {
      "id": "kb_collection",
      "action": "rag.collection_create",
      "params": {
        "name": "product_knowledge_base",
        "description": "Product documentation and FAQs for customer support AI"
      }
    },
    {
      "id": "user_memory_collection",
      "action": "rag.collection_create",
      "params": {
        "name": "user_conversation_memory",
        "description": "Per-user conversation history for personalized responses"
      }
    },
    {
      "id": "add_faq_doc",
      "action": "rag.document_add",
      "params": {
        "collection_id": {"from": "kb_collection.result.collection_id"},
        "text": "How to reset your password: Go to Settings → Security → Reset Password",
        "metadata": {"category": "account", "topic": "password_reset", "version": "1.0"}
      }
    },
    {
      "id": "add_user_memory",
      "action": "rag.memory_add",
      "params": {
        "collection_id": {"from": "user_memory_collection.result.collection_id"},
        "content": "User prefers concise answers and technical detail",
        "user_id": "$user_id"
      }
    },
    {
      "id": "test_search",
      "action": "rag.search",
      "params": {
        "collection_id": {"from": "kb_collection.result.collection_id"},
        "query": "how do I reset my password",
        "limit": 3
      }
    },
    {
      "id": "list_collections",
      "action": "rag.collection_list",
      "params": {}
    }
  ]
}
```

**RAG patterns:**
- `rag.document_add` + `rag.search` = semantic knowledge base
- `rag.memory_add` + `rag.memory_search` = per-user persistent memory
- `rag.memory_get` = retrieve all memories for a user
- Combine with `logic.create` to auto-index content on write events

---

## 9. Buff Templates Library

**Use when:** Pre-create all buff templates for a project so they can be instantly
applied to any user/project without re-creating.

```json
{
  "steps": [
    {
      "id": "free_tier",
      "action": "buffs.create_buff",
      "params": {
        "name": "Free Plan",
        "effects": {"plan": "free", "api_calls_limit": 100, "storage_mb": 100},
        "revert_on_expire": false
      }
    },
    {
      "id": "pro_trial",
      "action": "buffs.create_buff",
      "params": {
        "name": "Pro Trial",
        "duration_days": 14,
        "effects": {"plan": "pro", "api_calls_limit": 10000, "storage_mb": 10000},
        "revert_on_expire": true
      }
    },
    {
      "id": "pro_monthly",
      "action": "buffs.create_buff",
      "params": {
        "name": "Pro Monthly",
        "duration_days": 30,
        "effects": {"plan": "pro", "api_calls_limit": 50000, "storage_mb": 50000},
        "revert_on_expire": true,
        "on_expire": [{"command": "event.subscription_expired"}]
      }
    },
    {
      "id": "enterprise",
      "action": "buffs.create_buff",
      "params": {
        "name": "Enterprise",
        "effects": {"plan": "enterprise", "api_calls_limit": 999999, "storage_mb": 500000, "sla_guaranteed": true},
        "revert_on_expire": false
      }
    },
    {
      "id": "double_xp_event",
      "action": "buffs.create_buff",
      "params": {
        "name": "Double XP Event",
        "duration_days": 2,
        "effects": {"xp_multiplier": 2.0, "event_active": true},
        "revert_on_expire": true
      }
    },
    {
      "id": "welcome_bonus",
      "action": "buffs.create_buff",
      "params": {
        "name": "Welcome Bonus",
        "duration_days": 7,
        "effects": {"welcome": true, "bonus_multiplier": 1.5},
        "revert_on_expire": true
      }
    },
    {
      "id": "throttled",
      "action": "buffs.create_buff",
      "params": {
        "name": "Quota Throttle",
        "effects": {"rate_limit_rps": 1, "throttled": true},
        "revert_on_expire": false,
        "description": "Applied when user exceeds quota"
      }
    },
    {
      "id": "list_buffs",
      "action": "buffs.list_active_buffs",
      "params": {}
    }
  ]
}
```

**Applying a template to a user:**
```json
{
  "steps": [
    {
      "id": "apply",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": "$pro_trial_buff_id",
        "entity_id": "$user_id",
        "entity_kind": "user"
      }
    }
  ]
}
```

---

## 10. Complete Production Bootstrap

**Use when:** Setting up a complete production project — runs all playbooks above
in the correct order in one session.

```json
{
  "steps": [
    {
      "id": "project",
      "action": "projects.create_project_anonymous",
      "params": {
        "name": "MyApp Production",
        "data": {"config": {"env": "production", "version": "1.0"}}
      }
    },
    {
      "id": "credits_asset",
      "action": "assets.create",
      "params": {"name": "Credits", "type": "currency", "data": {"symbol": "CR", "earnable": true}}
    },
    {
      "id": "main_wallet",
      "action": "wallets.create",
      "params": {"name": "Main Wallet", "currency": "credits"}
    },
    {
      "id": "seed_wallet",
      "action": "wallets.deposit",
      "params": {"wallet_id": {"from": "main_wallet.result.wallet_id"}, "amount": 1000, "description": "Seed"}
    },
    {
      "id": "fap_defaults",
      "action": "data_access.apply_defaults_template",
      "params": {}
    },
    {
      "id": "free_buff",
      "action": "buffs.create_buff",
      "params": {"name": "Free Plan", "effects": {"plan": "free", "api_calls_limit": 100}, "revert_on_expire": false}
    },
    {
      "id": "pro_buff",
      "action": "buffs.create_buff",
      "params": {"name": "Pro Plan", "duration_days": 30, "effects": {"plan": "pro", "api_calls_limit": 50000}, "revert_on_expire": true}
    },
    {
      "id": "counter_rule",
      "action": "logic.create",
      "params": {
        "name": "API call counter",
        "triggers": [{"type": "command", "command": "system.api_call_authenticated"}],
        "space": [{"processor": "field_operations_processor", "action": "add",
          "parameters": {"field1": "user.data.api_calls_month", "field2": 1, "result_field": "user.data.api_calls_month"}}],
        "enabled": true
      }
    },
    {
      "id": "daily_cleanup",
      "action": "scheduler.create_task",
      "params": {"name": "Daily cleanup", "task_type": "cron", "cron_expression": "0 4 * * *", "payload": {"action": "cleanup_sessions"}}
    },
    {
      "id": "monthly_billing",
      "action": "scheduler.create_task",
      "params": {"name": "Monthly billing", "task_type": "cron", "cron_expression": "0 10 1 * *", "payload": {"action": "process_billing"}}
    },
    {
      "id": "monthly_reset",
      "action": "scheduler.create_task",
      "params": {"name": "Monthly reset", "task_type": "cron", "cron_expression": "0 0 1 * *", "payload": {"action": "reset_counters"}}
    },
    {
      "id": "agent_key",
      "action": "apikeys.create",
      "params": {"name": "AI Agent Key", "service_caps": ["payments", "scheduler"]}
    },
    {
      "id": "analytics_key",
      "action": "apikeys.create",
      "params": {"name": "Analytics Key", "service_caps": ["analytics"]}
    },
    {
      "id": "kb_collection",
      "action": "rag.collection_create",
      "params": {"name": "app_knowledge_base", "description": "App documentation for AI support"}
    },
    {
      "id": "final_check",
      "action": "projects.get_stats",
      "params": {}
    }
  ]
}
```

**Output:** 15 steps, fully provisioned project with economy, FAP, buff templates,
rules, scheduler, 2 API keys, RAG collection — ready for users.

---

## 11. Mass Operations Patterns

### 11.1 Bulk-apply buff to many users

Use `commands.execute_batch` for O(N) users without N round-trips:

```json
{
  "steps": [
    {
      "id": "batch_apply",
      "action": "commands.execute_batch",
      "params": {
        "commands": "$user_ids_array",
        "template": {
          "command_type": "buff",
          "command_name": "apply_buff",
          "payload": {
            "buff_id": "$welcome_buff_id",
            "entity_id": "$item",
            "entity_kind": "user"
          }
        }
      }
    }
  ]
}
```

### 11.2 Bulk-update user data field

```json
{
  "steps": [
    {
      "id": "reset_streaks",
      "action": "commands.execute_batch",
      "params": {
        "commands": "$user_ids",
        "template": {
          "command_type": "dna_crud",
          "command_name": "update_user",
          "payload": {
            "target_entity": "data_users_user",
            "operation_type": "update",
            "filters": {"user_id": "$item"},
            "updates": {"data.streak": 0}
          }
        }
      }
    }
  ]
}
```

### 11.3 Clone buff templates from another project

```json
{
  "steps": [
    {
      "id": "list_source",
      "action": "buffs.list_active_buffs",
      "params": {"project_id": "$source_project_id"}
    },
    {
      "id": "create_copy",
      "action": "buffs.create_buff",
      "params": {
        "name": {"from": "list_source.result.buffs[0].name"},
        "effects": {"from": "list_source.result.buffs[0].effects"},
        "duration_days": {"from": "list_source.result.buffs[0].duration_days"}
      }
    }
  ]
}
```

### 11.4 Migrate all users to new plan buff

```json
{
  "steps": [
    {
      "id": "get_users",
      "action": "projects.get_users",
      "params": {}
    },
    {
      "id": "remove_old_plan",
      "action": "commands.execute_batch",
      "params": {
        "commands": "$user_ids",
        "template": {
          "command_type": "buff",
          "command_name": "cancel_buff",
          "payload": {"buff_id": "$old_plan_buff_id", "entity_id": "$item"}
        }
      }
    },
    {
      "id": "apply_new_plan",
      "action": "commands.execute_batch",
      "params": {
        "commands": "$user_ids",
        "template": {
          "command_type": "buff",
          "command_name": "apply_buff",
          "payload": {"buff_id": "$new_plan_buff_id", "entity_id": "$item", "entity_kind": "user"}
        }
      }
    }
  ]
}
```

### 11.5 Bulk logic rules update (enable/disable)

```json
{
  "steps": [
    {
      "id": "list_rules",
      "action": "logic.list",
      "params": {}
    },
    {
      "id": "disable_rule",
      "action": "logic.update",
      "params": {"rule_id": "$rule_id", "enabled": false}
    },
    {
      "id": "enable_rule",
      "action": "logic.update",
      "params": {"rule_id": "$other_rule_id", "enabled": true}
    }
  ]
}
```

### 11.6 Multi-environment key rotation

```json
{
  "steps": [
    {
      "id": "list_old_keys",
      "action": "apikeys.list",
      "params": {}
    },
    {
      "id": "delete_old",
      "action": "apikeys.delete",
      "params": {"key_id": "$old_key_id"}
    },
    {
      "id": "new_prod_key",
      "action": "apikeys.create",
      "params": {"name": "Production AI Agent (rotated)", "service_caps": ["payments", "scheduler"]}
    },
    {
      "id": "new_staging_key",
      "action": "apikeys.create",
      "params": {"name": "Staging Key (rotated)", "service_caps": ["analytics"]}
    }
  ]
}
```

---

## Step variables reference

| Variable pattern                    | Meaning                                          |
|-------------------------------------|--------------------------------------------------|
| `{"from": "stepId.result.field"}`   | Value from a prior step result                   |
| `"$variable_name"`                  | Caller-supplied value (replace before sending)   |
| `"if": {"from": ..., "op": ...}`    | Conditional step execution                       |
| `"options.stopOnError": false`      | Continue batch even if one step fails            |
| `"options.continueOnError": true`   | Same as above (alias)                            |
| `"options.async": true`             | Run long batch in background, return `job_id`    |

**Operators for `if`:** `equals`, `not_equals`, `in`, `not_in`, `gte`, `lte`, `gt`, `lt`, `exists`

---

## Reference

- Full action catalog: `GET /mcp/actions`
- Business flow examples: `docs/MCP_BUSINESS_FLOWS.md`
- Commerce map: `docs/MCP_ECONOMY_AND_COMMERCE_MAP.md`
- API key safety: `docs/API_KEY_SERVICE_CAPS.md`
- Field access policy: `docs/FIELD_ACCESS_POLICY.md`
