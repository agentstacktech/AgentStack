# MCP - Complex project examples

Demonstration of synergy between AgentStack systems via MCP tools. These examples show how to combine Buff System, Projects, Payments, Scheduler, Analytics and other modules to build full-featured projects.

## Contents

1. [SaaS platform with subscriptions](#1-saas-platform-with-subscriptions)
2. [Game with progress and monetization](#2-game-with-progress-and-monetization)
3. [Marketplace with promo campaigns](#3-marketplace-with-promo-campaigns)
4. [Education platform with trials](#4-education-platform-with-trials)
5. [Enterprise system with analytics](#5-enterprise-system-with-analytics)

---

## 1. SaaS platform with subscriptions

**Goal:** Build a full SaaS platform with automatic trials, subscriptions, payments and analytics.

### Stage 1: Project creation and setup

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MySaaS Platform",
      "description": "SaaS platform with subscriptions"
    }
  },
  {
    "tool": "projects.create_api_key",
    "params": {
      "project_id": "<project_id>",
      "name": "Main API Key"
    }
  }
]
```

**Result:** Project created, API key obtained.

### Stage 2: Trial period for new users

```json
[
  {
    "tool": "logic.create",
    "params": {
      "project_id": "<project_id>",
      "name": "Auto-trial on signup",
      "triggers": [
        {
          "type": "event",
          "event": "user.created"
        }
      ],
      "space": [
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "buffs.apply_temporary_effect",
            "params": {
              "entity_id": "{{user_id}}",
              "entity_kind": "user",
              "name": "Welcome 7-Day Trial",
              "duration_days": 7,
              "effects": {
                "data.limits.api_calls": 10000,
                "data.limits.logic_engine_calls": 5000,
                "data.features.premium_trial": true
              },
              "priority": 100
            }
          }
        }
      ]
    }
  }
]
```

**Result:** Every new user automatically gets a 7-day trial.

### Stage 3: Subscriptions and payment integration

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Premium Monthly Subscription",
      "type": "subscription",
      "category": "subscription",
      "duration_days": 30,
      "priority": 200,
      "effects": {
        "data.limits.api_calls": 50000,
        "data.limits.logic_engine_calls": 25000,
        "data.features.premium": true,
        "data.features.advanced_analytics": true
      },
      "config": {
        "revert_on_expire": false,
        "persistent": false,
        "extends_on_reapply": true
      }
    }
  },
  {
    "tool": "payments.create_payment",
    "params": {
      "project_id": "<project_id>",
      "merchant_id": 123,
      "amount_minor": 2900,
      "currency": "USD",
      "description": "Premium Monthly Subscription",
      "payment_method": "CARD"
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

**Result:** User paid for subscription and received premium features.

### Stage 4: Automatic subscription renewal

```json
[
  {
    "tool": "scheduler.schedule_task",
    "params": {
      "project_id": "<project_id>",
      "name": "Auto-renew subscriptions",
      "schedule": "0 0 1 * *",
      "endpoint": "/api/buffs/auto-renew",
      "method": "POST",
      "payload": {
        "action": "check_and_renew"
      }
    }
  },
  {
    "tool": "logic.create",
    "params": {
      "project_id": "<project_id>",
      "name": "Subscription renewal handler",
      "triggers": [
        {
          "type": "webhook",
          "endpoint": "/api/buffs/auto-renew"
        }
      ],
      "space": [
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "buffs.list_active_buffs",
            "params": {
              "entity_kind": "user",
              "category": "subscription"
            }
          }
        },
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "buffs.extend_buff",
            "params": {
              "buff_id": "{{buff_id}}",
              "entity_id": "{{user_id}}",
              "entity_kind": "user",
              "additional_days": 30
            }
          }
        }
      ]
    }
  }
]
```

**Result:** Subscriptions renew automatically every month.

### Stage 5: Usage analytics

```json
[
  {
    "tool": "analytics.get_usage",
    "params": {
      "project_id": "<project_id>",
      "period": "month",
      "metrics": ["api_calls", "logic_engine_calls", "active_users"]
    }
  },
  {
    "tool": "analytics.get_metrics",
    "params": {
      "project_id": "<project_id>",
      "metric_type": "subscriptions",
      "period": "month"
    }
  }
]
```

**Result:** Full usage and subscription analytics.

---

## 2. Game with progress and monetization

**Goal:** Build a game with progress system, in-app purchases and seasonal events.

### Stage 1: Game project creation

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MyGame",
      "description": "Mobile game with progress"
    }
  }
]
```

### Stage 2: Starter pack for new players

```json
[
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": "<new_player_id>",
      "entity_kind": "user",
      "name": "Starter Pack",
      "effects": {
        "data.game.currency.gold": 1000,
        "data.game.currency.gems": 100,
        "data.game.items.starter_weapon": true,
        "data.game.level": 1
      },
      "priority": 300
    }
  }
]
```

**Result:** New player receives the starter pack.

### Stage 3: Seasonal event (temporary buff)

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<project_id>",
      "entity_kind": "project",
      "name": "Summer Event 2024",
      "type": "promo",
      "category": "event",
      "duration_days": 14,
      "priority": 200,
      "effects": {
        "data.game.events.summer_active": true,
        "data.game.bonuses.xp_multiplier": 2.0,
        "data.game.bonuses.gold_multiplier": 1.5
      },
      "config": {
        "revert_on_expire": true
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<event_buff_id>",
      "entity_id": "<project_id>",
      "entity_kind": "project"
    }
  }
]
```

**Result:** All players receive bonuses from the seasonal event.

### Stage 4: In-game currency purchase

```json
[
  {
    "tool": "payments.create_payment",
    "params": {
      "project_id": "<project_id>",
      "merchant_id": "<player_id>",
      "amount_minor": 999,
      "currency": "USD",
      "description": "1000 Gems Pack",
      "payment_method": "CARD"
    }
  },
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": "<player_id>",
      "entity_kind": "user",
      "name": "Gems Purchase - 1000",
      "effects": {
        "data.game.currency.gems": 1000
      },
      "priority": 250
    }
  }
]
```

**Result:** Player purchased in-game currency.

### Stage 5: Game premium subscription

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<player_id>",
      "entity_kind": "user",
      "name": "Game Premium Pass",
      "type": "subscription",
      "category": "subscription",
      "duration_days": 30,
      "priority": 200,
      "effects": {
        "data.game.features.premium_pass": true,
        "data.game.bonuses.daily_rewards": true,
        "data.game.bonuses.ad_free": true,
        "data.game.currency.gems_daily": 50
      },
      "config": {
        "extends_on_reapply": true
      }
    }
  },
  {
    "tool": "scheduler.schedule_task",
    "params": {
      "project_id": "<project_id>",
      "name": "Daily premium rewards",
      "schedule": "0 0 * * *",
      "endpoint": "/api/game/daily-premium-rewards",
      "method": "POST"
    }
  }
]
```

**Result:** Premium subscription with daily rewards.

---

## 3. Marketplace with promo campaigns

**Goal:** Build a marketplace with automatic promos, discounts and sales analytics.

### Stage 1: Marketplace project creation

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MyMarketplace",
      "description": "Marketplace with promo campaigns"
    }
  }
]
```

### Stage 2: Black Friday - promo for all sellers

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<project_id>",
      "entity_kind": "project",
      "name": "Black Friday 2024",
      "type": "promo",
      "category": "event",
      "duration_days": 7,
      "priority": 200,
      "effects": {
        "data.marketplace.discount.global": 0.5,
        "data.marketplace.fee.reduction": 0.3,
        "data.marketplace.features.priority_listing": true
      },
      "config": {
        "revert_on_expire": true
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<promo_buff_id>",
      "entity_id": "<project_id>",
      "entity_kind": "project"
    }
  },
  {
    "tool": "notifications.send_notification",
    "params": {
      "project_id": "<project_id>",
      "type": "broadcast",
      "message": "Black Friday has started! Up to 50% off!"
    }
  }
]
```

**Result:** All sellers get discounts and reduced fees.

### Stage 3: Premium subscription for sellers

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<seller_id>",
      "entity_kind": "user",
      "name": "Seller Premium",
      "type": "subscription",
      "category": "subscription",
      "duration_days": 30,
      "priority": 200,
      "effects": {
        "data.marketplace.features.premium_seller": true,
        "data.marketplace.limits.listings": 1000,
        "data.marketplace.fee.reduction": 0.2,
        "data.marketplace.features.analytics_advanced": true
      },
      "config": {
        "extends_on_reapply": true
      }
    }
  }
]
```

**Result:** Seller receives premium features.

### Stage 4: Sales and promo effectiveness analytics

```json
[
  {
    "tool": "analytics.get_metrics",
    "params": {
      "project_id": "<project_id>",
      "metric_type": "sales",
      "period": "week",
      "filters": {
        "promo_active": true
      }
    }
  },
  {
    "tool": "analytics.export_data",
    "params": {
      "project_id": "<project_id>",
      "format": "csv",
      "data_type": "transactions",
      "period": "month"
    }
  }
]
```

**Result:** Full promo campaign effectiveness analytics.

---

## 4. Education platform with trials

**Goal:** Build an education platform with automatic course trials and subscriptions.

### Stage 1: Project creation

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "EduPlatform",
      "description": "Education platform"
    }
  }
]
```

### Stage 2: Automatic course trial on signup

```json
[
  {
    "tool": "logic.create",
    "params": {
      "project_id": "<project_id>",
      "name": "Auto-trial course on signup",
      "triggers": [
        {
          "type": "event",
          "event": "user.created"
        }
      ],
      "space": [
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "buffs.apply_temporary_effect",
            "params": {
              "entity_id": "{{user_id}}",
              "entity_kind": "user",
              "name": "Welcome Course Trial",
              "duration_days": 14,
              "effects": {
                "data.courses.access.trial": true,
                "data.courses.limit.trial_courses": 3,
                "data.features.trial_active": true
              },
              "priority": 100"
            }
          }
        }
      ]
    }
  }
]
```

**Result:** New users automatically get a 14-day trial.

### Stage 3: Premium subscription with access to all courses

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<student_id>",
      "entity_kind": "user",
      "name": "Premium All Courses",
      "type": "subscription",
      "category": "subscription",
      "duration_days": 30,
      "priority": 200,
      "effects": {
        "data.courses.access.all": true,
        "data.courses.features.certificates": true,
        "data.courses.features.priority_support": true,
        "data.courses.limit.courses_unlimited": true
      },
      "config": {
        "extends_on_reapply": true
      }
    }
  }
]
```

**Result:** Student gets access to all courses.

### Stage 4: Seasonal promo - subscription discount

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": "<project_id>",
      "entity_kind": "project",
      "name": "Back to School 2024",
      "type": "promo",
      "category": "event",
      "duration_days": 30,
      "priority": 200,
      "effects": {
        "data.subscription.discount": 0.4,
        "data.subscription.features.bonus_months": 1
      },
      "config": {
        "revert_on_expire": true
      }
    }
  }
]
```

**Result:** 40% discount on subscriptions for one month.

---

## 5. Enterprise system with analytics

**Goal:** Build an enterprise system with automatic limit management, analytics and notifications.

### Stage 1: Enterprise project creation

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "Enterprise System",
      "description": "Enterprise platform with analytics"
    }
  }
]
```

### Stage 2: Automatic limit increase when threshold is reached

```json
[
  {
    "tool": "logic.create",
    "params": {
      "project_id": "<project_id>",
      "name": "Auto-increase limits on threshold",
      "triggers": [
        {
          "type": "metric",
          "metric": "api_calls_usage",
          "threshold": 0.8
        }
      ],
      "space": [
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "analytics.get_usage",
            "params": {
              "project_id": "<project_id>",
              "period": "month"
            }
          }
        },
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "buffs.apply_temporary_effect",
            "params": {
              "entity_id": "<project_id>",
              "entity_kind": "project",
              "name": "Auto-burst capacity",
              "duration_days": 7,
              "effects": {
                "data.limits.api_calls": 100000
              },
              "priority": 150
            }
          }
          "condition": "{{usage_percent}} > 80"
        },
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "notifications.send_notification",
            "params": {
              "project_id": "<project_id>",
              "type": "alert",
              "message": "Limits increased automatically for 7 days"
            }
          }
        }
      ]
    }
  }
]
```

**Result:** System automatically increases limits under high usage.

### Stage 3: Monitoring and reports

```json
[
  {
    "tool": "scheduler.schedule_task",
    "params": {
      "project_id": "<project_id>",
      "name": "Weekly analytics report",
      "schedule": "0 9 * * 1",
      "endpoint": "/api/analytics/weekly-report",
      "method": "POST"
    }
  },
  {
    "tool": "logic.create",
    "params": {
      "project_id": "<project_id>",
      "name": "Generate weekly report",
      "triggers": [
        {
          "type": "webhook",
          "endpoint": "/api/analytics/weekly-report"
        }
      ],
      "space": [
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "analytics.get_usage",
            "params": {
              "project_id": "<project_id>",
              "period": "week"
            }
          }
        },
        {
          "processor": "mcp_command_processor",
          "action": "execute",
          "parameters": {
            "tool": "analytics.export_data",
            "params": {
              "project_id": "<project_id>",
              "format": "csv",
              "data_type": "usage",
              "period": "week"
            }
          }
        }
      ]
    }
  }
]
```

**Result:** Weekly reports are generated automatically.

---

## Key synergy patterns

### Pattern 1: Buffs + Logic Engine = Automation
Combine buffs with Logic Engine to apply effects automatically on events.

### Pattern 2: Buffs + Scheduler = Periodic effects
Use the scheduler for automatic subscription renewal and seasonal events.

### Pattern 3: Buffs + Payments = Monetization
Integrate payments with buffs for subscriptions and one-time purchases.

### Pattern 4: Buffs + Analytics = Optimization
Track buff effectiveness via analytics and optimize offerings.

### Pattern 5: Buffs + Notifications = Notifications
Notify users about new buffs, trial expiry and promo campaigns.

---

**Last updated:** 2025-01-20  
**Version:** 1.0.0
