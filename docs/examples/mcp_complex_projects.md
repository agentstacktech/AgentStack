# MCP - Примеры комплексных проектов

Демонстрация синергии между различными системами AgentStack через MCP инструменты. Эти примеры показывают, как комбинировать Buff System, Projects, Payments, Scheduler, Analytics и другие модули для создания полноценных проектов.

## Содержание

1. [SaaS платформа с подписками](#1-saas-платформа-с-подписками)
2. [Игра с прогрессом и монетизацией](#2-игра-с-прогрессом-и-монетизацией)
3. [Маркетплейс с промо-кампаниями](#3-маркетплейс-с-промо-кампаниями)
4. [Образовательная платформа с триалами](#4-образовательная-платформа-с-триалами)
5. [Enterprise система с аналитикой](#5-enterprise-система-с-аналитикой)

---

## 1. SaaS платформа с подписками

**Цель:** Создать полноценную SaaS платформу с автоматическими триалами, подписками, платежами и аналитикой.

### Этап 1: Создание проекта и настройка

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MySaaS Platform",
      "description": "SaaS платформа с подписками"
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

**Результат:** Проект создан, API ключ получен.

### Этап 2: Настройка триального периода для новых пользователей

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

**Результат:** Каждый новый пользователь автоматически получает 7-дневный триал.

### Этап 3: Создание подписок и интеграция с платежами

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

**Результат:** Пользователь оплатил подписку и получил премиум функции.

### Этап 4: Автоматическое продление подписок

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

**Результат:** Подписки автоматически продлеваются каждый месяц.

### Этап 5: Аналитика использования

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

**Результат:** Полная аналитика использования и подписок.

---

## 2. Игра с прогрессом и монетизацией

**Цель:** Создать игру с системой прогресса, внутриигровыми покупками и сезонными событиями.

### Этап 1: Создание проекта игры

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MyGame",
      "description": "Мобильная игра с прогрессом"
    }
  }
]
```

### Этап 2: Настройка стартового пакета для новых игроков

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

**Результат:** Новый игрок получает стартовый набор.

### Этап 3: Сезонное событие (временный бафф)

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

**Результат:** Все игроки получают бонусы от сезонного события.

### Этап 4: Покупка внутриигровой валюты

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

**Результат:** Игрок купил внутриигровую валюту.

### Этап 5: Премиум подписка для игры

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

**Результат:** Премиум подписка с ежедневными наградами.

---

## 3. Маркетплейс с промо-кампаниями

**Цель:** Создать маркетплейс с автоматическими промо-акциями, скидками и аналитикой продаж.

### Этап 1: Создание проекта маркетплейса

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "MyMarketplace",
      "description": "Маркетплейс с промо-кампаниями"
    }
  }
]
```

### Этап 2: Черная пятница - промо-кампания для всех продавцов

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
      "message": "Black Friday началась! Скидки до 50%!"
    }
  }
]
```

**Результат:** Все продавцы получают скидки и сниженные комиссии.

### Этап 3: Премиум подписка для продавцов

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

**Результат:** Продавец получает премиум функции.

### Этап 4: Аналитика продаж и промо-эффективности

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

**Результат:** Полная аналитика эффективности промо-кампаний.

---

## 4. Образовательная платформа с триалами

**Цель:** Создать образовательную платформу с автоматическими триалами курсов и подписками.

### Этап 1: Создание проекта

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "EduPlatform",
      "description": "Образовательная платформа"
    }
  }
]
```

### Этап 2: Автоматический триал курса при регистрации

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

**Результат:** Новые пользователи автоматически получают 14-дневный триал.

### Этап 3: Премиум подписка с доступом ко всем курсам

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

**Результат:** Студент получает доступ ко всем курсам.

### Этап 4: Сезонная акция - скидка на подписку

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

**Результат:** Скидка 40% на подписки в течение месяца.

---

## 5. Enterprise система с аналитикой

**Цель:** Создать enterprise систему с автоматическим управлением лимитами, аналитикой и уведомлениями.

### Этап 1: Создание enterprise проекта

```json
[
  {
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "Enterprise System",
      "description": "Enterprise платформа с аналитикой"
    }
  }
]
```

### Этап 2: Автоматическое увеличение лимитов при достижении порога

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
              "message": "Лимиты увеличены автоматически на 7 дней"
            }
          }
        }
      ]
    }
  }
]
```

**Результат:** Система автоматически увеличивает лимиты при высоком использовании.

### Этап 3: Мониторинг и отчеты

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

**Результат:** Еженедельные отчеты генерируются автоматически.

---

## Ключевые паттерны синергии

### Паттерн 1: Buffs + Logic Engine = Автоматизация
Комбинируйте бафы с Logic Engine для автоматического применения эффектов при событиях.

### Паттерн 2: Buffs + Scheduler = Периодические эффекты
Используйте планировщик для автоматического продления подписок и сезонных событий.

### Паттерн 3: Buffs + Payments = Монетизация
Интегрируйте платежи с бафами для продажи подписок и разовых покупок.

### Паттерн 4: Buffs + Analytics = Оптимизация
Отслеживайте эффективность бафов через аналитику и оптимизируйте предложения.

### Паттерн 5: Buffs + Notifications = Уведомления
Уведомляйте пользователей о новых бафах, истечении триалов и промо-акциях.

---

**Последнее обновление:** 2025-01-20  
**Версия:** 1.0.0
