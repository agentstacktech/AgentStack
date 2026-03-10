# MCP Buffs - Постоянные эффекты

Примеры использования MCP инструментов для применения постоянных эффектов (подписки, разовые покупки, пакеты улучшений).

## Содержание

1. [Подписки](#подписки)
2. [Разовые покупки](#разовые-покупки)
3. [Пакеты улучшений](#пакеты-улучшений)
4. [Workflows](#workflows)
5. [Обработка ошибок](#обработка-ошибок)

---

## Подписки

### Пример 1: Месячная подписка Premium

**Сценарий:** Создать и применить месячную подписку с автоматическим продлением.

**Шаг 1: Создание подписки**
```json
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
}
```

**Шаг 2: Применение подписки**
```json
{
  "tool": "buffs.apply_buff",
  "params": {
    "buff_id": "<subscription_buff_id>",
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

**Шаг 3: Продление подписки (через месяц)**
```json
{
  "tool": "buffs.extend_buff",
  "params": {
    "buff_id": "<subscription_buff_id>",
    "entity_id": 123,
    "entity_kind": "user",
    "additional_days": 30
  }
}
```

### Пример 2: Годовая подписка

```json
{
  "tool": "buffs.create_buff",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Premium Annual Subscription",
    "type": "subscription",
    "category": "subscription",
    "duration_days": 365,
    "priority": 250,
    "effects": {
      "data.limits.api_calls": 600000,
      "data.features.premium": true,
      "data.subscription.tier": "annual"
    },
    "config": {
      "extends_on_reapply": true
    }
  }
}
```

---

## Разовые покупки

### Пример 1: Lifetime Premium

**Сценарий:** Применить постоянное улучшение, которое никогда не истекает.

**Вариант 1: Через create + apply**
```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Lifetime Premium",
      "type": "purchase",
      "category": "permanent",
      "duration_days": 365,
      "priority": 300,
      "effects": {
        "data.features.premium": true,
        "data.limits.api_calls": 1000000,
        "data.purchase.lifetime": true
      },
      "config": {
        "persistent": true,
        "revert_on_expire": false
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

**Вариант 2: Быстрое применение (одним шагом)**
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
    },
    "priority": 300
  }
}
```

### Пример 2: Пакет дополнительных лимитов

```json
{
  "tool": "buffs.apply_persistent_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "API Calls Expansion Pack",
    "effects": {
      "data.limits.api_calls": 500000
    },
    "priority": 250
  }
}
```

---

## Пакеты улучшений

### Пример 1: Стартовый пакет

**Сценарий:** Применить набор постоянных улучшений для нового пользователя.

```json
{
  "tool": "buffs.apply_persistent_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Starter Pack",
    "effects": {
      "data.limits.api_calls": 100000,
      "data.limits.logic_engine_calls": 50000,
      "data.features.advanced_analytics": true,
      "data.features.custom_domains": true
    },
    "priority": 200
  }
}
```

### Пример 2: Бизнес-пакет

```json
{
  "tool": "buffs.apply_persistent_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Business Pack",
    "effects": {
      "data.limits.api_calls": 1000000,
      "data.limits.logic_engine_calls": 500000,
      "data.features.premium": true,
      "data.features.advanced_analytics": true,
      "data.features.custom_domains": true,
      "data.features.white_label": true,
      "data.features.priority_support": true
    },
    "priority": 300
  }
}
```

---

## Workflows

### Workflow 1: Создание и применение подписки

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Premium Monthly",
      "type": "subscription",
      "category": "subscription",
      "duration_days": 30,
      "effects": {"data.features.premium": true}
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
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

### Workflow 2: Продление подписки

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
  },
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "<subscription_buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

### Workflow 3: Применение постоянного улучшения

```json
[
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
  },
  {
    "tool": "buffs.list_active_buffs",
    "params": {
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

### Workflow 4: Отмена постоянного баффа (требуются admin права)

```json
[
  {
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "uuid-here",
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.cancel_buff",
    "params": {
      "buff_id": "uuid-here",
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

**Важно:** Для постоянных баффов в состоянии ACTIVE требуется admin/owner права. Cancel автоматически выполнит revert перед удалением.

---

## Обработка ошибок

### Ошибка: Persistent buffs cannot be reverted

**Причина:** Попытка откатить постоянный бафф.

**Решение:** Постоянные баффы не откатываются. Используйте `buffs.cancel_buff` для удаления (требуются admin права).

### Ошибка: Insufficient permissions to cancel active buff

**Причина:** Попытка отменить активный бафф без admin/owner прав.

**Решение:** 
- Для PENDING баффов - обычные права достаточно
- Для ACTIVE баффов - требуются admin/owner права
- Проверьте права через `projects.get_project` или используйте другой подход

### Ошибка: Buff does not support extension

**Причина:** Попытка продлить бафф, который не поддерживает продление.

**Решение:** Проверьте `config.extends_on_reapply` через `buffs.get_buff`. Если false, создайте новый бафф вместо продления.

---

## Best Practices

1. **Используйте persistent=true** для постоянных эффектов:
   ```json
   {"config": {"persistent": true, "revert_on_expire": false}}
   ```

2. **Устанавливайте высокий приоритет** для постоянных баффов:
   - 300+ для lifetime purchases
   - 250+ для годовых подписок
   - 200+ для месячных подписок

3. **Мониторьте подписки** через регулярные проверки:
   ```json
   {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}}
   ```

4. **Автоматизируйте продление** через планировщик:
   - Используйте `scheduler.schedule_task` для автоматического продления
   - Проверяйте `expires_at` перед истечением

5. **Комбинируйте баффы** для накопительных эффектов:
   - Несколько постоянных баффов суммируются
   - Используйте `buffs.get_effective_limits` для проверки итоговых лимитов

---

## Дополнительные примеры

### Пример: Накопительные пакеты

Применение нескольких постоянных баффов для накопительного эффекта:

```json
[
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "API Calls Pack 1",
      "effects": {"data.limits.api_calls": 100000}
    }
  },
  {
    "tool": "buffs.apply_persistent_effect",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "API Calls Pack 2",
      "effects": {"data.limits.api_calls": 100000}
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

Итоговый лимит будет суммой всех баффов.

### Пример: Подписка с автоматическим продлением

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Auto-Renewing Subscription",
      "type": "subscription",
      "duration_days": 30,
      "effects": {"data.features.premium": true},
      "config": {
        "extends_on_reapply": true
      }
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  }
]
```

Используйте `scheduler.schedule_task` для автоматического продления через `buffs.extend_buff` каждые 30 дней.
