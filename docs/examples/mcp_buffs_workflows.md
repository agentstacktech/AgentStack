# MCP Buffs - Workflows и комбинации инструментов

Готовые workflows и комбинации MCP инструментов для управления баффами.

## Содержание

1. [Базовые workflows](#базовые-workflows)
2. [Продвинутые workflows](#продвинутые-workflows)
3. [Комбинации инструментов](#комбинации-инструментов)
4. [Реальные сценарии](#реальные-сценарии)
5. [Best Practices](#best-practices)

---

## Базовые workflows

### Workflow 1: Полный цикл триального периода

**Описание:** Создание, применение и мониторинг триального баффа.

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
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
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 1,
      "entity_kind": "user"
    }
  }
]
```

### Workflow 2: Продление подписки

**Описание:** Поиск активной подписки и её продление.

```json
[
  {
    "tool": "buffs.list_active_buffs",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "category": "subscription",
      "project_id": 1
    }
  },
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "<subscription_buff_id from previous>",
      "entity_id": 123,
      "entity_kind": "user",
      "project_id": 1
    }
  },
  {
    "tool": "buffs.extend_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "additional_days": 30,
      "project_id": 1
    }
  },
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "project_id": 1
    }
  }
]
```

### Workflow 3: Отмена ошибочного баффа

**Описание:** Проверка, откат и удаление ошибочного баффа.

```json
[
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "error-buff-uuid",
      "entity_id": 123,
      "entity_kind": "user",
      "project_id": 1
    }
  },
  {
    "tool": "buffs.revert_buff",
    "params": {
      "buff_id": "error-buff-uuid",
      "entity_id": 123,
      "entity_kind": "user",
      "project_id": 1
    }
  },
  {
    "tool": "buffs.cancel_buff",
    "params": {
      "buff_id": "error-buff-uuid",
      "entity_id": 123,
      "entity_kind": "user",
      "project_id": 1
    }
  }
]
```

### Workflow 4: Создание и применение промо-акции

**Описание:** Создание промо-баффа для проекта и проверка эффектов.

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 1,
      "entity_kind": "project",
      "name": "Holiday Sale 2024",
      "type": "promo",
      "category": "event",
      "duration_days": 14,
      "priority": 200,
      "effects": {
        "data.limits.api_calls": 50000,
        "data.stats.discount": 0.3
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<promo_buff_id>",
      "entity_id": 1,
      "entity_kind": "project"
    }
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 1,
      "entity_kind": "project"
    }
  },
  {
    "tool": "buffs.list_active_buffs",
    "params": {
      "entity_id": 1,
      "entity_kind": "project"
    }
  }
]
```

---

## Продвинутые workflows

### Workflow 5: Конвертация триала в подписку

**Описание:** После истечения триала создание и применение подписки.

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
        "data.features.premium": true
      },
      "config": {
        "extends_on_reapply": true
      }
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

### Workflow 6: Применение нескольких баффов

**Описание:** Создание и применение нескольких баффов для комбинированного эффекта.

```json
[
  {
    "tool": "buffs.apply_temporary_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Weekend Bonus",
      "duration_days": 2,
      "effects": {"data.limits.api_calls": 5000},
      "priority": 150
    }
  },
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Premium Upgrade",
      "effects": {"data.features.premium": true},
      "priority": 300
    }
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

### Workflow 7: Мониторинг и управление баффами

**Описание:** Полный цикл мониторинга активных баффов и управления ими.

```json
[
  {
    "tool": "buffs.list_active_buffs",
    "params": {
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "<each_active_buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

---

## Комбинации инструментов

### Комбинация 1: Create + Apply = Полный цикл применения

**Использование:** Когда нужно создать шаблон и сразу применить.

```json
[
  {"tool": "buffs.create_buff", "params": {...}},
  {"tool": "buffs.apply_buff", "params": {"buff_id": "<from previous>"}}
]
```

**Альтернатива:** Используйте `buffs.apply_temporary_effect` или `buffs.apply_persistent_effect` для одного шага.

### Комбинация 2: List + Extend = Продление подписки

**Использование:** Найти активную подписку и продлить её.

```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.extend_buff", "params": {"buff_id": "<from previous>"}}
]
```

### Комбинация 3: Get + Revert + Cancel = Полная отмена

**Использование:** Проверить состояние, откатить эффекты и удалить бафф.

```json
[
  {"tool": "buffs.get_buff", "params": {...}},
  {"tool": "buffs.revert_buff", "params": {...}},
  {"tool": "buffs.cancel_buff", "params": {...}}
]
```

**Примечание:** Для ACTIVE баффов `buffs.cancel_buff` автоматически выполняет revert перед удалением.

### Комбинация 4: Apply + Get Effective Limits = Проверка результатов

**Использование:** Применить бафф и сразу проверить изменения в лимитах.

```json
[
  {"tool": "buffs.apply_buff", "params": {...}},
  {"tool": "buffs.get_effective_limits", "params": {...}}
]
```

### Комбинация 5: List + Get = Детальный анализ

**Использование:** Получить список активных баффов и детальную информацию о каждом.

```json
[
  {"tool": "buffs.list_active_buffs", "params": {...}},
  {"tool": "buffs.get_buff", "params": {"buff_id": "<each_buff_id>"}}
]
```

---

## Реальные сценарии

### Сценарий 1: SaaS подписка с автоматическим продлением

**Задача:** Создать систему подписок с автоматическим продлением через планировщик.

**Шаг 1: Создание подписки**
```json
{
  "tool": "buffs.create_buff",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Premium Monthly",
    "type": "subscription",
    "category": "subscription",
    "duration_days": 30,
    "effects": {"data.features.premium": true},
    "config": {"extends_on_reapply": true}
  }
}
```

**Шаг 2: Применение**
```json
{
  "tool": "buffs.apply_buff",
  "params": {
    "buff_id": "<subscription_buff_id>",
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```

**Шаг 3: Настройка автоматического продления (через планировщик)**
```json
{
  "tool": "scheduler.schedule_task",
  "params": {
    "project_id": 1,
    "name": "Auto-renew subscription",
    "schedule": "0 0 1 * *",
    "endpoint": "/api/buffs/{buff_id}/extend",
    "method": "POST",
    "payload": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "additional_days": 30
    }
  }
}
```

### Сценарий 2: Промо-кампания для группы пользователей

**Задача:** Применить промо-бафф ко всему проекту (влияет на всех пользователей).

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 456,
      "entity_kind": "project",
      "name": "Black Friday 2024",
      "type": "promo",
      "category": "event",
      "duration_days": 7,
      "priority": 200,
      "effects": {
        "data.limits.api_calls": 100000,
        "data.stats.discount": 0.5
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<promo_buff_id>",
      "entity_id": 456,
      "entity_kind": "project"
    }
  }
]
```

### Сценарий 3: Пакетная покупка улучшений

**Задача:** Применить несколько постоянных улучшений для накопительного эффекта.

```json
[
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "API Calls Pack 1",
      "effects": {"data.limits.api_calls": 100000},
      "priority": 250
    }
  },
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "API Calls Pack 2",
      "effects": {"data.limits.api_calls": 100000},
      "priority": 250
    }
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

Итоговый лимит: 200,000 API calls (сумма всех баффов).

### Сценарий 4: Отмена ошибочного баффа с проверкой

**Задача:** Безопасно отменить активный бафф с проверкой возможности отката.

```json
[
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "error-buff-uuid",
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.revert_buff",
    "params": {
      "buff_id": "error-buff-uuid",
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.get_effective_limits",
    "params": {
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

Если revert не удался (недостаточно ресурсов), будет ошибка с деталями.

---

## Best Practices

### 1. Всегда проверяйте состояние перед операциями

```json
{"tool": "buffs.get_buff", "params": {...}}
```

### 2. Используйте категории для организации

- `"subscription"` - для подписок
- `"event"` - для промо-акций
- `"trial"` - для триалов
- `"permanent"` - для постоянных улучшений

### 3. Устанавливайте правильные приоритеты

- 100-200: Обычные баффы
- 200-300: Важные баффы (промо, подписки)
- 300+: Критичные баффы (постоянные улучшения)

### 4. Проверяйте эффективные лимиты после изменений

```json
{"tool": "buffs.get_effective_limits", "params": {...}}
```

### 5. Мониторьте истечение баффов

Регулярно проверяйте `expires_at` через `buffs.list_active_buffs` и автоматически продлевайте подписки.

### 6. Используйте быстрые методы для простых случаев

- `buffs.apply_temporary_effect` - для одноразовых временных эффектов
- `buffs.apply_persistent_effect` - для постоянных улучшений
- `buffs.create_buff + buffs.apply_buff` - когда нужен шаблон для повторного использования

### 7. Обрабатывайте ошибки gracefully

- Проверяйте `buff_not_found` - используйте `buffs.list_active_buffs` для поиска
- Проверяйте `insufficient_permissions` - для ACTIVE баффов требуются admin права
- Проверяйте `revert_failed` - ресурсы не могут быть откачены

---

## Интеграция с другими инструментами

### Интеграция с планировщиком

Автоматическое продление подписок:

```json
{
  "tool": "scheduler.schedule_task",
  "params": {
    "name": "Renew subscription",
    "schedule": "0 0 1 * *",
    "endpoint": "/api/buffs/{buff_id}/extend",
    "payload": {
      "buff_id": "<subscription_buff_id>",
      "additional_days": 30
    }
  }
}
```

### Интеграция с Logic Engine

Автоматическое применение баффов при событиях:

```json
{
  "tool": "logic.create",
  "params": {
    "name": "Auto-apply trial on signup",
    "triggers": [{"type": "event", "event": "user.signup"}],
    "space": [{
      "processor": "mcp_command_processor",
      "action": "execute",
      "parameters": {
        "tool": "buffs.apply_temporary_effect",
        "params": {
          "entity_id": "{{user_id}}",
          "entity_kind": "user",
          "name": "Welcome Trial",
          "duration_days": 7,
          "effects": {"data.limits.api_calls": 1000}
        }
      }
    }]
  }
}
```

---

## Частые ошибки и решения

### Ошибка: "Revert failed: insufficient resources"

**Причина:** Бафф добавил ресурсы, которые были потрачены, и откат невозможен.

**Решение:** 
- Проверьте текущее состояние через `buffs.get_buff`
- Используйте `buffs.cancel_buff` с admin правами для принудительной отмены
- Или дождитесь истечения баффа (автоматический revert)

### Ошибка: "Buff does not support extension"

**Причина:** `config.extends_on_reapply` = false.

**Решение:** Создайте новый бафф вместо продления существующего.

### Ошибка: "Insufficient permissions to cancel active buff"

**Причина:** Попытка отменить ACTIVE бафф без admin/owner прав.

**Решение:** 
- Для PENDING баффов - обычные права достаточно
- Для ACTIVE баффов - требуются admin/owner права
- Используйте `buffs.revert_buff` отдельно, если у вас нет прав на cancel

---

## Продвинутые техники

### Техника 1: Накопительные эффекты

Применение нескольких баффов для суммирования эффектов:

```json
[
  {"tool": "buffs.apply_persistent_effect", "params": {"effects": {"data.limits.api_calls": 100000}}},
  {"tool": "buffs.apply_persistent_effect", "params": {"effects": {"data.limits.api_calls": 100000}}},
  {"tool": "buffs.get_effective_limits", "params": {...}}
]
```

Итоговый лимит будет суммой всех баффов.

### Техника 2: Условное применение

Проверка существующих баффов перед применением:

```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.create_buff", "params": {...}},  // Только если нет активной подписки
  {"tool": "buffs.apply_buff", "params": {...}}
]
```

### Техника 3: Групповое управление

Применение баффов к нескольким пользователям:

```json
[
  {"tool": "projects.get_users", "params": {"project_id": 1}},
  {"tool": "buffs.apply_temporary_effect", "params": {"entity_id": "<each_user_id>", ...}}
]
```

---

**Последнее обновление:** 2025-01-20
**Версия:** 1.0.0
