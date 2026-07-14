# MCP Business Flows — Real Scenarios with Tool Synergies

**Audience:** AI agents using `agentstack.execute` via MCP  
**How to use:** Find the scenario closest to your task, adapt the JSON, run it.  
**All actions** are valid `step.action` values unless marked `REST-only`.  
**Full action catalog:** `GET /mcp/actions`

---

## Index

1. [Complete Project Bootstrap](#1-complete-project-bootstrap)
2. [SaaS Trial → Subscription → Upgrade](#2-saas-trial--subscription--upgrade)
3. [Game Economy — Currencies, Wallets, Rewards](#3-game-economy--currencies-wallets-rewards)
4. [Loyalty & Rewards Program](#4-loyalty--rewards-program)
5. [E-commerce Checkout Flow](#5-e-commerce-checkout-flow)
6. [AI Automation Pipeline](#6-ai-automation-pipeline)
7. [Content Platform with Access Control](#7-content-platform-with-access-control)
8. [Social Community Activation](#8-social-community-activation)
9. [Multi-tenant B2B Onboarding](#9-multi-tenant-b2b-onboarding)
10. [Event-driven Marketing Campaign](#10-event-driven-marketing-campaign)
11. [Usage-based Billing with Auto-throttling](#11-usage-based-billing-with-auto-throttling)
12. [Player Matchmaking & Seasonal Events](#12-player-matchmaking--seasonal-events)

---

## 1. Complete Project Bootstrap

**Problem:** Start from zero — provision a project with economy, RBAC, logic rules,
API keys, and scheduled maintenance in a single session.

**Tool chain:** `projects.create_project_anonymous` → `assets.create` ×N →
`wallets.create` → `wallets.deposit` → `buffs.create_buff` (welcome) →
`logic.create` (rules) → `scheduler.create_task` (maintenance) →
`apikeys.create` (restricted) → `rbac.assign_role`

```json
{
  "steps": [
    {
      "id": "project",
      "action": "projects.create_project_anonymous",
      "params": {
        "name": "My App",
        "data": {
          "config": {"currency": "credits", "version": "1.0"}
        }
      }
    },
    {
      "id": "asset_credits",
      "action": "assets.create",
      "params": {
        "name": "Credits",
        "type": "currency",
        "project_id": {"from": "project.result.project_id"},
        "data": {"symbol": "CR", "decimals": 0}
      }
    },
    {
      "id": "asset_premium",
      "action": "assets.create",
      "params": {
        "name": "Premium Gems",
        "type": "currency",
        "project_id": {"from": "project.result.project_id"},
        "data": {"symbol": "GEM", "decimals": 0, "premium": true}
      }
    },
    {
      "id": "wallet",
      "action": "wallets.create",
      "params": {
        "name": "Main Wallet",
        "currency": "credits",
        "project_id": {"from": "project.result.project_id"}
      }
    },
    {
      "id": "seed_balance",
      "action": "wallets.deposit",
      "params": {
        "wallet_id": {"from": "wallet.result.wallet_id"},
        "amount": 1000,
        "description": "Initial seed balance"
      }
    },
    {
      "id": "welcome_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Welcome Bonus",
        "description": "7-day welcome period with boosted rates",
        "duration_days": 7,
        "effects": {"xp_multiplier": 2.0, "welcome": true},
        "revert_on_expire": true,
        "project_id": {"from": "project.result.project_id"}
      }
    },
    {
      "id": "daily_reward_rule",
      "action": "logic.create",
      "params": {
        "name": "Daily Login Reward",
        "description": "Award credits on daily login",
        "triggers": [{"type": "command", "command": "event.daily_login"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.credits", "field2": 10, "result_field": "user.data.credits"}
          }
        ],
        "enabled": true,
        "project_id": {"from": "project.result.project_id"}
      }
    },
    {
      "id": "cleanup_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Weekly Cleanup",
        "task_type": "cron",
        "cron_expression": "0 3 * * 0",
        "payload": {"action": "cleanup_expired_sessions"},
        "project_id": {"from": "project.result.project_id"}
      }
    },
    {
      "id": "agent_key",
      "action": "apikeys.create",
      "params": {
        "name": "AI Agent Key",
        "service_caps": ["payments", "scheduler"],
        "project_id": {"from": "project.result.project_id"}
      }
    }
  ]
}
```

**Key synergies:**

- `project_id` flows from step 1 through all subsequent steps via `{"from": "..."}`
- `assets.create` x2 = multi-currency economy in one batch
- `buffs.create_buff` without `apply` = template ready for all new users
- `apikeys.create` with `service_caps` = safe agent key that can pay + schedule

---

## 2. SaaS Trial → Subscription → Upgrade

**Problem:** User starts a free trial, converts to paid, and later upgrades their plan.
Each transition must be atomic with automatic revert on failure.

### Phase A — Start Trial

```json
{
  "steps": [
    {
      "id": "trial_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Pro Trial",
        "duration_days": 14,
        "effects": {
          "plan": "pro",
          "api_calls_limit": 10000,
          "team_members_limit": 5
        },
        "revert_on_expire": true,
        "on_expire": [{"command": "event.trial_expired", "payload": {"user_id": "$user_id"}}]
      }
    },
    {
      "id": "apply_trial",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": {"from": "trial_buff.result.buff_id"},
        "entity_id": "$user_id",
        "entity_kind": "user"
      }
    },
    {
      "id": "trial_reminder",
      "action": "scheduler.create_task",
      "params": {
        "name": "Trial expiry reminder",
        "task_type": "once",
        "run_at": "$trial_end_minus_2days",
        "payload": {"template": "trial_expiring_soon", "user_id": "$user_id"}
      }
    }
  ]
}
```

### Phase B — Convert to Paid

```json
{
  "steps": [
    {
      "id": "charge",
      "action": "payments.create",
      "params": {
        "amount": 29.0,
        "currency": "USD",
        "description": "Pro Plan - Monthly",
        "user_id": "$user_id",
        "metadata": {"plan": "pro", "period": "monthly"}
      }
    },
    {
      "id": "remove_trial",
      "action": "buffs.revert_buff",
      "params": {"application_id": "$trial_application_id"},
      "if": {"from": "charge.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "pro_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Pro Subscription",
        "duration_days": 30,
        "effects": {"plan": "pro", "api_calls_limit": 50000, "team_members_limit": 20},
        "revert_on_expire": true,
        "on_expire": [{"command": "event.subscription_expired"}]
      },
      "if": {"from": "charge.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "apply_pro",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": {"from": "pro_buff.result.buff_id"},
        "entity_id": "$user_id",
        "entity_kind": "user"
      },
      "if": {"from": "charge.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "renewal_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly renewal",
        "task_type": "once",
        "run_at": "$next_billing_date",
        "payload": {"action": "process_renewal", "user_id": "$user_id", "plan": "pro"}
      },
      "if": {"from": "charge.result.status", "op": "equals", "value": "completed"}
    }
  ]
}
```

### Phase C — Plan Upgrade (Pro → Enterprise)

```json
{
  "steps": [
    {
      "id": "prorate_charge",
      "action": "payments.create",
      "params": {
        "amount": 71.0,
        "currency": "USD",
        "description": "Upgrade Pro → Enterprise (prorated)",
        "metadata": {"from_plan": "pro", "to_plan": "enterprise"}
      }
    },
    {
      "id": "cancel_pro",
      "action": "buffs.cancel_buff",
      "params": {"application_id": "$pro_application_id"},
      "if": {"from": "prorate_charge.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "enterprise_buff",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": "$enterprise_buff_template_id",
        "entity_id": "$user_id",
        "entity_kind": "user"
      },
      "if": {"from": "prorate_charge.result.status", "op": "equals", "value": "completed"}
    }
  ]
}
```

**Key synergies:**

- `payments.create` + `if` condition = payment-gated buff activation
- `buffs.revert_buff` on old plan + `buffs.apply_buff` new plan = seamless upgrade
- `scheduler.create_task` for each future event = event-driven billing

---

## 3. Game Economy — Currencies, Wallets, Rewards

**Problem:** Set up a game economy with soft/hard currencies, reward logic,
daily bonuses, and in-app purchases.

```json
{
  "steps": [
    {
      "id": "soft_currency",
      "action": "assets.create",
      "params": {"name": "Gold", "type": "currency", "data": {"symbol": "G", "earnable": true}}
    },
    {
      "id": "hard_currency",
      "action": "assets.create",
      "params": {"name": "Gems", "type": "currency", "data": {"symbol": "GEM", "purchasable": true, "premium": true}}
    },
    {
      "id": "player_wallet",
      "action": "wallets.create",
      "params": {"name": "Player Wallet", "currency": "gold"}
    },
    {
      "id": "starter_bonus",
      "action": "wallets.deposit",
      "params": {
        "wallet_id": {"from": "player_wallet.result.wallet_id"},
        "amount": 500,
        "description": "Starter gold bonus"
      }
    },
    {
      "id": "daily_reward_logic",
      "action": "logic.create",
      "params": {
        "name": "Daily Login Reward",
        "triggers": [{"type": "command", "command": "game.daily_login"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.gold", "field2": 50, "result_field": "user.data.gold"}
          },
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.daily_streak", "field2": 1, "result_field": "user.data.daily_streak"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "streak_bonus_logic",
      "action": "logic.create",
      "params": {
        "name": "Streak Bonus (7-day)",
        "triggers": [{"type": "command", "command": "game.daily_login"}],
        "conditions": [{"field": "user.data.daily_streak", "op": "equals", "value": 7}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.gems", "field2": 10, "result_field": "user.data.gems"}
          },
          {
            "processor": "field_operations_processor",
            "action": "set",
            "parameters": {"field": "user.data.daily_streak", "value": 0}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "iap_gem_pack",
      "action": "assets.create",
      "params": {
        "name": "Gem Pack 100",
        "type": "iap_product",
        "data": {"gems_amount": 100, "price_usd": 0.99, "sku": "gems_100"}
      }
    },
    {
      "id": "seasonal_event_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Double XP Weekend",
        "duration_days": 2,
        "effects": {"xp_multiplier": 2.0, "gold_multiplier": 1.5},
        "revert_on_expire": true
      }
    }
  ]
}
```

**Purchasing gems (IAP flow):**

```json
{
  "steps": [
    {
      "id": "pay",
      "action": "payments.create",
      "params": {"amount": 0.99, "currency": "USD", "description": "Gem Pack 100", "metadata": {"sku": "gems_100"}}
    },
    {
      "id": "deposit_gems",
      "action": "wallets.deposit",
      "params": {
        "wallet_id": "$user_gem_wallet_id",
        "amount": 100,
        "description": "Gem Pack 100 purchase"
      },
      "if": {"from": "pay.result.status", "op": "equals", "value": "completed"}
    }
  ]
}
```

**Key synergies:**

- `assets.create` defines economy schema; `wallets.deposit` funds it
- `logic.create` with conditions = streak/combo reward engine
- `payments.create` + `if` + `wallets.deposit` = verified IAP flow
- `buffs.create_buff` as event template = activate for all users with one `apply_buff`

---

## 4. Loyalty & Rewards Program

**Problem:** Track points, award badges, tier-based perks, point expiry,
and redemption for any type of app.

```json
{
  "steps": [
    {
      "id": "points_asset",
      "action": "assets.create",
      "params": {"name": "Loyalty Points", "type": "points", "data": {"symbol": "LP", "expiry_days": 365}}
    },
    {
      "id": "bronze_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Bronze Member",
        "effects": {"tier": "bronze", "discount_pct": 5, "priority_support": false},
        "revert_on_expire": false
      }
    },
    {
      "id": "silver_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Silver Member",
        "effects": {"tier": "silver", "discount_pct": 10, "priority_support": true},
        "revert_on_expire": false
      }
    },
    {
      "id": "gold_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Gold Member",
        "effects": {"tier": "gold", "discount_pct": 20, "priority_support": true, "free_shipping": true},
        "revert_on_expire": false
      }
    },
    {
      "id": "earn_points_rule",
      "action": "logic.create",
      "params": {
        "name": "Earn points on purchase",
        "triggers": [{"type": "command", "command": "event.purchase_completed"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {
              "field1": "user.data.loyalty_points",
              "field2": {"from_event": "purchase.amount_usd"},
              "result_field": "user.data.loyalty_points"
            }
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "tier_upgrade_rule",
      "action": "logic.create",
      "params": {
        "name": "Auto tier upgrade",
        "triggers": [{"type": "field_change", "field": "user.data.loyalty_points"}],
        "conditions": [{"field": "user.data.loyalty_points", "op": "gte", "value": 500}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "set",
            "parameters": {"field": "user.data.tier", "value": "silver"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "expiry_scheduler",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly points expiry check",
        "task_type": "cron",
        "cron_expression": "0 2 1 * *",
        "payload": {"action": "expire_loyalty_points", "expiry_days": 365}
      }
    }
  ]
}
```

**Key synergies:**

- `assets.create` = points schema with metadata
- `buffs.create_buff` x3 = tier templates, apply instantly to any user
- `logic.create` with `field_change` trigger = reactive tier upgrades
- `scheduler.create_task` with cron = automatic expiry without extra code

---

## 5. E-commerce Checkout Flow

**Problem:** Accept payment, update inventory, apply loyalty points, and
trigger fulfillment — all in one atomic session.

```json
{
  "steps": [
    {
      "id": "check_stock",
      "action": "commands.execute",
      "params": {
        "command_type": "dna_crud",
        "command_name": "check_inventory",
        "payload": {
          "target_entity": "data_projects_8dna",
          "operation_type": "get",
          "filters": {"product_id": "$product_id"}
        }
      }
    },
    {
      "id": "payment",
      "action": "payments.create",
      "params": {
        "amount": "$order_total",
        "currency": "USD",
        "description": "Order #$order_id",
        "metadata": {"order_id": "$order_id", "product_ids": "$product_ids"}
      },
      "if": {"from": "check_stock.result.in_stock", "op": "equals", "value": true}
    },
    {
      "id": "decrement_stock",
      "action": "commands.execute",
      "params": {
        "command_type": "dna_crud",
        "command_name": "update_inventory",
        "payload": {
          "target_entity": "data_projects_8dna",
          "operation_type": "update",
          "filters": {"product_id": "$product_id"},
          "updates": {"stock": {"decrement": "$quantity"}}
        }
      },
      "if": {"from": "payment.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "add_loyalty_points",
      "action": "wallets.deposit",
      "params": {
        "wallet_id": "$user_loyalty_wallet_id",
        "amount": {"from": "payment.result.amount"},
        "description": "Loyalty points for order"
      },
      "if": {"from": "payment.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "fulfillment_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Fulfill order $order_id",
        "task_type": "once",
        "run_at": "now",
        "payload": {"action": "trigger_fulfillment", "order_id": "$order_id", "payment_id": {"from": "payment.result.payment_id"}}
      },
      "if": {"from": "payment.result.status", "op": "equals", "value": "completed"}
    },
    {
      "id": "record_purchase",
      "action": "analytics.get_metrics",
      "params": {"metric": "revenue", "event": "purchase_completed", "amount": "$order_total"}
    }
  ]
}
```

**Key synergies:**

- `commands.execute` for stock check → `payments.create` with `if` = stock-gated payment
- `wallets.deposit` = loyalty points on every purchase (no extra service)
- `scheduler.create_task` run_at=now = async fulfillment trigger
- `analytics.get_metrics` = automatic revenue tracking

---

## 6. AI Automation Pipeline

**Problem:** Build a self-maintaining pipeline: monitor usage, auto-scale limits,
alert on thresholds, and run cleanup tasks.

```json
{
  "steps": [
    {
      "id": "usage_monitor_rule",
      "action": "logic.create",
      "params": {
        "name": "API quota monitor",
        "triggers": [{"type": "command", "command": "system.api_call_completed"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "project.data.api_calls_today", "field2": 1, "result_field": "project.data.api_calls_today"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "threshold_alert_rule",
      "action": "logic.create",
      "params": {
        "name": "Usage threshold alert",
        "triggers": [{"type": "field_change", "field": "project.data.api_calls_today"}],
        "conditions": [{"field": "project.data.api_calls_today", "op": "gte", "value": 8000}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "set",
            "parameters": {"field": "project.data.quota_warning_sent", "value": true}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "auto_scale_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Emergency quota boost",
        "duration_days": 1,
        "effects": {"api_calls_limit": 20000, "overage_mode": true},
        "revert_on_expire": true
      }
    },
    {
      "id": "daily_reset_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Daily API counter reset",
        "task_type": "cron",
        "cron_expression": "0 0 * * *",
        "payload": {
          "action": "reset_counter",
          "fields": ["project.data.api_calls_today", "project.data.quota_warning_sent"]
        }
      }
    },
    {
      "id": "weekly_analytics_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Weekly usage report",
        "task_type": "cron",
        "cron_expression": "0 9 * * 1",
        "payload": {"action": "generate_usage_report", "send_email": true}
      }
    },
    {
      "id": "stale_data_cleanup",
      "action": "scheduler.create_task",
      "params": {
        "name": "Stale session cleanup",
        "task_type": "cron",
        "cron_expression": "0 4 * * *",
        "payload": {"action": "cleanup_expired_sessions", "older_than_days": 30}
      }
    }
  ]
}
```

**Key synergies:**

- `logic.create` x2 = counter + threshold alert without a single line of server code
- `buffs.create_buff` (emergency boost) ready to apply when `threshold_alert_rule` fires
- `scheduler.create_task` x3 = full maintenance calendar
- All steps reference `project_id` from context — no explicit passing needed

---

## 7. Content Platform with Access Control

**Problem:** Create a content library, gate premium content behind subscriptions,
track consumption, and manage editorial permissions.

```json
{
  "steps": [
    {
      "id": "free_tier_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Free Access",
        "effects": {"content_tier": "free", "articles_per_month": 3, "downloads": 0},
        "revert_on_expire": false
      }
    },
    {
      "id": "premium_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Premium Subscription",
        "duration_days": 30,
        "effects": {"content_tier": "premium", "articles_per_month": 999, "downloads": 50},
        "revert_on_expire": true,
        "on_expire": [{"command": "event.premium_expired"}]
      }
    },
    {
      "id": "content_asset_pdf",
      "action": "assets.create",
      "params": {
        "name": "Annual Report 2025",
        "type": "document",
        "data": {"tier": "premium", "format": "pdf", "file_size_mb": 4.2}
      }
    },
    {
      "id": "article_limit_rule",
      "action": "logic.create",
      "params": {
        "name": "Article consumption tracker",
        "triggers": [{"type": "command", "command": "content.article_read"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.articles_read_this_month", "field2": 1, "result_field": "user.data.articles_read_this_month"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "fap_policy",
      "action": "data_access.set_policy",
      "params": {
        "resources": {
          "premium_content": {
            "default_access": "deny",
            "fields": {
              "body": {"rule": "allow_if", "condition": {"field": "user.data.content_tier", "op": "equals", "value": "premium"}},
              "preview": {"rule": "allow"}
            }
          }
        }
      }
    },
    {
      "id": "editor_role",
      "action": "rbac.assign_role",
      "params": {"user_id": "$editor_user_id", "role": "editor"}
    },
    {
      "id": "monthly_reset",
      "action": "scheduler.create_task",
      "params": {
        "name": "Reset monthly article counts",
        "task_type": "cron",
        "cron_expression": "0 0 1 * *",
        "payload": {"action": "reset_monthly_counters", "field": "user.data.articles_read_this_month"}
      }
    }
  ]
}
```

**Key synergies:**

- `buffs.create_buff` (free/premium templates) + `data_access.set_policy` = content gating without custom middleware
- `assets.create` = content catalog with metadata
- `logic.create` counter + `scheduler.create_task` reset = metered access model
- `rbac.assign_role` = editorial permissions

---

## 8. Social Community Activation

**Problem:** Onboard a user into a social community: create profile, join a channel,
send first welcome message, connect with community bot.

```json
{
  "steps": [
    {
      "id": "publish_profile",
      "action": "social.pas.public_me_put",
      "params": {
        "display_name": "$user_name",
        "bio": "$bio",
        "avatar_url": "$avatar_url",
        "tags": ["new_member"]
      }
    },
    {
      "id": "join_general",
      "action": "social.channel_invites.redeem",
      "params": {"token": "$general_channel_invite_token"}
    },
    {
      "id": "welcome_message",
      "action": "social.chat.post",
      "params": {
        "channel_id": "$general_channel_id",
        "text": "Hey everyone! I just joined.",
        "kind": "text"
      }
    },
    {
      "id": "friend_bot",
      "action": "social.friends.request",
      "params": {"target_user_id": "$community_bot_user_id"}
    },
    {
      "id": "index_profile",
      "action": "social.public.publish",
      "params": {
        "data": {
          "display_name": "$user_name",
          "tags": ["active", "new_member"],
          "joined_at": "$now"
        }
      }
    },
    {
      "id": "onboarding_buff",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": "$onboarding_buff_template_id",
        "entity_id": "$user_id",
        "entity_kind": "user"
      }
    }
  ]
}
```

**RAG-enhanced community search:**

```json
{
  "steps": [
    {
      "id": "index_message",
      "action": "rag.document_add",
      "params": {
        "collection_id": "$community_rag_collection_id",
        "text": "$message_text",
        "metadata": {"author": "$user_id", "channel": "$channel_id", "ts": "$timestamp"}
      }
    },
    {
      "id": "search",
      "action": "rag.search",
      "params": {
        "collection_id": "$community_rag_collection_id",
        "query": "how to get started with the API",
        "limit": 5
      }
    }
  ]
}
```

**Key synergies:**

- `social.pas.public_me_put` + `social.public.publish` = profile visible in global index
- `social.channel_invites.redeem` = join any channel with single-use token
- `buffs.apply_buff` = onboarding perks applied automatically
- `rag.document_add` on chat messages = searchable community knowledge base

---

## 9. Multi-tenant B2B Onboarding

**Problem:** Enterprise sale — provision a new tenant organization, set up their team,
configure feature flags per contract tier, and hand off API credentials.

```json
{
  "steps": [
    {
      "id": "org_project",
      "action": "projects.create_project",
      "params": {
        "name": "$company_name Workspace",
        "data": {
          "config": {
            "org_id": "$crm_org_id",
            "contract_tier": "enterprise",
            "seats": 50
          }
        }
      }
    },
    {
      "id": "admin_role",
      "action": "rbac.assign_role",
      "params": {
        "user_id": "$admin_user_id",
        "role": "owner",
        "project_id": {"from": "org_project.result.project_id"}
      }
    },
    {
      "id": "enterprise_buff",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": "$enterprise_tier_buff_id",
        "entity_id": {"from": "org_project.result.project_id"},
        "entity_kind": "project"
      }
    },
    {
      "id": "sso_key",
      "action": "apikeys.create",
      "params": {
        "name": "$company_name SSO Integration",
        "service_caps": ["analytics"],
        "project_id": {"from": "org_project.result.project_id"}
      }
    },
    {
      "id": "agent_key",
      "action": "apikeys.create",
      "params": {
        "name": "$company_name AI Agent",
        "service_caps": ["payments", "scheduler"],
        "project_id": {"from": "org_project.result.project_id"}
      }
    },
    {
      "id": "invite_team",
      "action": "social.channel_invites.direct",
      "params": {
        "user_ids": "$team_user_ids",
        "channel_id": "$org_channel_id",
        "message": "Welcome to $company_name workspace!"
      }
    },
    {
      "id": "fap_defaults",
      "action": "data_access.apply_defaults_template",
      "params": {"project_id": {"from": "org_project.result.project_id"}}
    },
    {
      "id": "health_check_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Weekly tenant health check",
        "task_type": "cron",
        "cron_expression": "0 8 * * 1",
        "payload": {"action": "tenant_health_report", "project_id": {"from": "org_project.result.project_id"}}
      }
    }
  ]
}
```

**Key synergies:**

- `projects.create_project` + `rbac.assign_role` = full org with owner in 2 steps
- `buffs.apply_buff` on `entity_kind: "project"` = project-level enterprise features
- `apikeys.create` x2 with different `service_caps` = SSO key (analytics only) + AI key (payments + scheduler)
- `data_access.apply_defaults_template` = instant policy without manual setup

---

## 10. Event-driven Marketing Campaign

**Problem:** Run a time-limited campaign: bonus credits for purchases, double XP
weekend, leaderboard tracking, and automatic campaign end.

```json
{
  "steps": [
    {
      "id": "campaign_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Summer Campaign 2025",
        "duration_days": 7,
        "effects": {
          "bonus_credits_pct": 20,
          "xp_multiplier": 2.0,
          "campaign_active": true
        },
        "revert_on_expire": true,
        "on_expire": [{"command": "event.campaign_ended", "payload": {"campaign_id": "summer_2025"}}]
      }
    },
    {
      "id": "apply_to_project",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": {"from": "campaign_buff.result.buff_id"},
        "entity_id": "$project_id",
        "entity_kind": "project"
      }
    },
    {
      "id": "bonus_credits_rule",
      "action": "logic.create",
      "params": {
        "name": "Campaign purchase bonus",
        "triggers": [{"type": "command", "command": "event.purchase_completed"}],
        "conditions": [{"field": "project.data.campaign_active", "op": "equals", "value": true}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "multiply",
            "parameters": {
              "field1": "event.purchase_amount",
              "field2": 0.2,
              "result_field": "user.data.bonus_credits"
            }
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "leaderboard_rule",
      "action": "logic.create",
      "params": {
        "name": "Campaign leaderboard tracker",
        "triggers": [{"type": "command", "command": "event.xp_earned"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.campaign_score", "field2": {"from_event": "xp_amount"}, "result_field": "user.data.campaign_score"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "leaderboard_asset",
      "action": "assets.create",
      "params": {
        "name": "Campaign Leaderboard Trophy",
        "type": "achievement",
        "data": {"campaign": "summer_2025", "rank_required": 1}
      }
    },
    {
      "id": "campaign_end_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Campaign end: award trophies",
        "task_type": "once",
        "run_at": "$campaign_end_datetime",
        "payload": {"action": "award_campaign_trophies", "asset_id": {"from": "leaderboard_asset.result.asset_id"}}
      }
    }
  ]
}
```

**Key synergies:**

- `buffs.apply_buff` on project = global state flag readable by all logic rules
- `logic.create` with `conditions` reading buff effect = campaign-aware business logic
- `assets.create` (trophy) + `scheduler.create_task` (award) = automated prize distribution

---

## 11. Usage-based Billing with Auto-throttling

**Problem:** Measure API usage, bill on consumption, auto-downgrade on non-payment,
auto-upgrade limits when usage grows.

```json
{
  "steps": [
    {
      "id": "usage_counter_logic",
      "action": "logic.create",
      "params": {
        "name": "Increment monthly API counter",
        "triggers": [{"type": "command", "command": "system.api_call_authenticated"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.api_calls_this_month", "field2": 1, "result_field": "user.data.api_calls_this_month"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "overage_detect_logic",
      "action": "logic.create",
      "params": {
        "name": "Detect quota overage",
        "triggers": [{"type": "field_change", "field": "user.data.api_calls_this_month"}],
        "conditions": [{"field": "user.data.api_calls_this_month", "op": "gte", "value": "$plan_quota"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "set",
            "parameters": {"field": "user.data.quota_exceeded", "value": true}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "monthly_invoice_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly usage billing",
        "task_type": "cron",
        "cron_expression": "0 10 1 * *",
        "payload": {
          "action": "generate_and_charge_invoice",
          "metric_field": "user.data.api_calls_this_month",
          "rate_per_call": 0.001
        }
      }
    },
    {
      "id": "throttle_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Quota Exceeded — Throttled",
        "effects": {"rate_limit_rps": 1, "quota_exceeded": true},
        "revert_on_expire": false,
        "description": "Applied when user exceeds plan quota without upgrade"
      }
    },
    {
      "id": "reset_counter_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Monthly counter reset",
        "task_type": "cron",
        "cron_expression": "0 0 1 * *",
        "payload": {
          "action": "reset_field",
          "fields": ["user.data.api_calls_this_month", "user.data.quota_exceeded"]
        }
      }
    }
  ]
}
```

**Key synergies:**

- `logic.create` with `field_change` trigger = reactive throttling without polling
- `buffs.create_buff` (throttle template) = instant apply when overage detected
- `scheduler.create_task` x2 = billing + reset in one setup call

---

## 12. Player Matchmaking & Seasonal Events

**Problem:** Create a matchmaking system with ELO ratings, seasonal ranking resets,
and tiered seasonal rewards.

```json
{
  "steps": [
    {
      "id": "season_buff",
      "action": "buffs.create_buff",
      "params": {
        "name": "Season 1",
        "duration_days": 90,
        "effects": {"season": 1, "season_active": true, "elo_visible": true},
        "revert_on_expire": true,
        "on_expire": [{"command": "event.season_ended", "payload": {"season": 1}}]
      }
    },
    {
      "id": "apply_season",
      "action": "buffs.apply_buff",
      "params": {
        "buff_id": {"from": "season_buff.result.buff_id"},
        "entity_id": "$project_id",
        "entity_kind": "project"
      }
    },
    {
      "id": "elo_update_rule",
      "action": "logic.create",
      "params": {
        "name": "ELO update after match",
        "triggers": [{"type": "command", "command": "game.match_completed"}],
        "space": [
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.elo", "field2": {"from_event": "elo_delta"}, "result_field": "user.data.elo"}
          },
          {
            "processor": "field_operations_processor",
            "action": "add",
            "parameters": {"field1": "user.data.season_points", "field2": {"from_event": "season_points_earned"}, "result_field": "user.data.season_points"}
          }
        ],
        "enabled": true
      }
    },
    {
      "id": "gold_rank_reward",
      "action": "assets.create",
      "params": {
        "name": "Season 1 Gold Badge",
        "type": "badge",
        "data": {"season": 1, "rank": "gold", "elo_threshold": 1800}
      }
    },
    {
      "id": "season_end_task",
      "action": "scheduler.create_task",
      "params": {
        "name": "Season 1 end: distribute rewards + reset",
        "task_type": "once",
        "run_at": "$season_end_datetime",
        "payload": {
          "action": "season_end_processing",
          "rank_asset_id": {"from": "gold_rank_reward.result.asset_id"},
          "reset_field": "user.data.season_points"
        }
      }
    },
    {
      "id": "matchmaking_rag_collection",
      "action": "rag.collection_create",
      "params": {
        "name": "matchmaking_player_profiles",
        "description": "Player skill profiles for semantic matchmaking"
      }
    }
  ]
}
```

**Key synergies:**

- `buffs.apply_buff` on project = global season state, readable by all rules
- `logic.create` = ELO updates without server code
- `assets.create` (badge) + `scheduler.create_task` = automatic end-of-season awards
- `rag.collection_create` = semantic player matching (add player profiles, search by skill tags)

---

## 9. Project support (user → team)

**Problem:** A signed-in user needs to message a project's support team without joining a public channel.

**Flow:** Discover staff inbox capability via roles, read thread, send message (MCP mirrors REST).

```json
{
  "steps": [
    {
      "id": "roles",
      "action": "social.support.inbox",
      "params": {"project_id": "$home_project_id", "limit": 5}
    },
    {
      "id": "history",
      "action": "social.support.history",
      "params": {"project_id": "$home_project_id", "limit": 50}
    },
    {
      "id": "send",
      "action": "social.support.send",
      "params": {"project_id": "$home_project_id", "body": "I need help with billing."}
    }
  ]
}
```

> Staff-only listing requires API key cap `project_support` on `social.support.inbox`; send/history use session RBAC inside the tool.

---

## Cross-flow patterns

### Pattern A — Payment-gated feature activation

```
payments.create → (if completed) → buffs.apply_buff
```

### Pattern B — Reactive logic chain

```
logic.create (trigger: event) → field update → logic.create (trigger: field_change) → escalation
```

### Pattern C — Time-boxed campaign

```
buffs.create_buff (revert_on_expire) → buffs.apply_buff (entity_kind: project) → logic.create (reads buff effect)
```

### Pattern D — Scheduled economy

```
scheduler.create_task (cron) → wallets.deposit OR payments.create OR buffs.apply_buff
```

### Pattern E — Safe agent provisioning

```
apikeys.create (service_caps: ["payments"]) → use returned api_key for payment-only agent
```

### Pattern F — Content metering

```
logic.create (counter) → scheduler.create_task (monthly reset) + buffs.create_buff (limit template)
```

---

## 13. Marketplace storefront buy (REST + wallet)

**Problem:** Agent discovers a listing globally and completes purchase with internal USDT wallet.

**REST chain (not MCP step.action for listing lifecycle):**

1. `GET /api/commerce/storefront/listings?scope=global&q=sword`
2. `POST /api/commerce/checkout/sessions` + header `Idempotency-Key`
3. `POST /api/commerce/checkout/sessions/{id}/confirm`

```json
{
  "listing_uuid": "$listing_uuid",
  "rail": "wallet_internal"
}
```

**Merchant bulk (project-scoped):**

1. `assets.create` (MCP) for catalog rows
2. `POST /api/commerce/marketplace/listings/bulk` with `X-Project-ID`

**SDK:**

```typescript
await sdk.commerce.listStorefront({ scope: 'global' });
await sdk.commerce.checkout.createSession({ listing_uuid, rail: 'wallet_internal' });
```

---

## Reference

- Full action catalog: `GET /mcp/actions`
- Current key capabilities: `GET /mcp/discovery` → `api_key_context.service_caps`
- Project setup playbooks: `docs/MCP_PROJECT_SETUP_PLAYBOOKS.md`
- Commerce flows: `docs/MCP_ECONOMY_AND_COMMERCE_MAP.md`
- API key safety: `docs/API_KEY_SERVICE_CAPS.md`


