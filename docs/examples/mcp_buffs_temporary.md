# MCP Buffs - Временные эффекты

Примеры использования MCP инструментов для применения временных эффектов (триалы, промо-акции, бонусы).

## Содержание

1. [Триальные периоды](#триальные-периоды)
2. [Промо-акции](#промо-акции)
3. [Временные бонусы](#временные-бонусы)
4. [Workflows](#workflows)
5. [Обработка ошибок](#обработка-ошибок)

---

## Триальные периоды

### Пример 1: 7-дневный триал для нового пользователя

**Сценарий:** Предоставить новому пользователю 7-дневный триал с увеличенными лимитами.

**Шаг 1: Создание баффа**
```json
{
  "tool": "buffs.create_buff",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "7-Day Premium Trial",
    "type": "trial",
    "category": "subscription",
    "duration_days": 7,
    "priority": 100,
    "effects": {
      "data.limits.api_calls": 10000,
      "data.limits.logic_engine_calls": 5000,
      "data.features.premium": true
    },
    "config": {
      "revert_on_expire": true,
      "persistent": false
    }
  }
}
```

**Ответ:**
```json
{
  "success": true,
  "data": {
    "buff_id": "2da8980e-70db-45cc-95c6-288f65e3bd4c",
    "state": "pending",
    "created_at": "2025-01-20T10:00:00Z"
  }
}
```

**Шаг 2: Применение баффа**
```json
{
  "tool": "buffs.apply_buff",
  "params": {
    "buff_id": "2da8980e-70db-45cc-95c6-288f65e3bd4c",
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

**Ответ:**
```json
{
  "success": true,
  "data": {
    "buff_id": "2da8980e-70db-45cc-95c6-288f65e3bd4c",
    "state": "active",
    "applied_at": "2025-01-20T10:00:00Z",
    "expires_at": "2025-01-27T10:00:00Z"
  }
}
```

**Шаг 3: Проверка активных баффов**
```json
{
  "tool": "buffs.list_active_buffs",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "project_id": 1
  }
}
```

**Шаг 4: Проверка эффективных лимитов**
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

### Пример 2: Быстрое применение триала (одним шагом)

Используйте `buffs.apply_temporary_effect` для быстрого применения:

```json
{
  "tool": "buffs.apply_temporary_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "7-Day Premium Trial",
    "duration_days": 7,
    "effects": {
      "data.limits.api_calls": 10000,
      "data.features.premium": true
    },
    "priority": 100
  }
}
```

---

## Промо-акции

### Пример 1: Черная пятница - промо для проекта

**Сценарий:** Применить промо-бафф ко всему проекту на 30 дней.

**Шаг 1: Создание промо-баффа**
```json
{
  "tool": "buffs.create_buff",
  "params": {
    "entity_id": 456,
    "entity_kind": "project",
    "name": "Black Friday 2024",
    "type": "promo",
    "category": "event",
    "duration_days": 30,
    "priority": 200,
    "effects": {
      "data.limits.api_calls": 50000,
      "data.stats.discount": 0.5,
      "data.features.promo_active": true
    },
    "config": {
      "revert_on_expire": true,
      "persistent": false
    }
  }
}
```

**Шаг 2: Применение к проекту**
```json
{
  "tool": "buffs.apply_buff",
  "params": {
    "buff_id": "<promo_buff_id>",
    "entity_id": 456,
    "entity_kind": "project"
  }
}
```

### Пример 2: Сезонная акция

```json
{
  "tool": "buffs.apply_temporary_effect",
  "params": {
    "entity_id": 456,
    "entity_kind": "project",
    "name": "Holiday Sale 2024",
    "duration_days": 14,
    "effects": {
      "data.stats.discount": 0.3,
      "data.limits.api_calls": 30000
    },
    "priority": 200
  }
}
```

---

## Временные бонусы

### Пример 1: Выходные бонусы

**Сценарий:** Дать пользователю бонус на выходные (2 дня).

```json
{
  "tool": "buffs.apply_temporary_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Weekend Bonus",
    "duration_days": 2,
    "effects": {
      "data.limits.api_calls": 5000,
      "data.bonuses.weekend_active": true
    },
    "priority": 150
  }
}
```

### Пример 2: Событийный бонус

```json
{
  "tool": "buffs.apply_temporary_effect",
  "params": {
    "entity_id": 123,
    "entity_kind": "user",
    "name": "Launch Event Bonus",
    "duration_days": 7,
    "effects": {
      "data.limits.api_calls": 20000,
      "data.limits.logic_engine_calls": 10000,
      "data.bonuses.event_active": true
    }
  }
}
```

---

## Workflows

### Workflow 1: Полный цикл триального периода

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 1,
      "entity_kind": "user",
      "name": "Trial",
      "type": "trial",
      "category": "subscription",
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

### Workflow 2: Продление триала

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
    "tool": "buffs.get_buff",
    "params": {
      "buff_id": "<trial_buff_id>",
      "entity_id": 123,
      "entity_kind": "user"
    }
  },
  {
    "tool": "buffs.extend_buff",
    "params": {
      "buff_id": "<trial_buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "additional_days": 7
    }
  }
]
```

### Workflow 3: Применение промо-акции к проекту

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 1,
      "entity_kind": "project",
      "name": "Holiday Sale",
      "type": "promo",
      "category": "event",
      "duration_days": 14,
      "effects": {"data.stats.discount": 0.3}
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
  }
]
```

---

## Обработка ошибок

### Ошибка: Buff not found

**Причина:** Бафф не существует или был удален.

**Решение:**
```json
{
  "tool": "buffs.list_active_buffs",
  "params": {
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```
Проверьте список активных баффов, чтобы найти правильный buff_id.

### Ошибка: Buff not in PENDING state

**Причина:** Попытка применить бафф, который уже активен или в другом состоянии.

**Решение:**
```json
{
  "tool": "buffs.get_buff",
  "params": {
    "buff_id": "uuid-here",
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```
Проверьте состояние баффа перед применением.

### Ошибка: Entity not found

**Причина:** Пользователь или проект не существует.

**Решение:** Убедитесь, что entity_id и entity_kind правильные. Для пользователей убедитесь, что project_id указан.

---

## Best Practices

1. **Всегда проверяйте состояние баффа** перед операциями:
   ```json
   {"tool": "buffs.get_buff", "params": {...}}
   ```

2. **Используйте категории** для организации баффов:
   - "subscription" - для подписок
   - "event" - для промо-акций
   - "trial" - для триалов

3. **Проверяйте эффективные лимиты** после применения:
   ```json
   {"tool": "buffs.get_effective_limits", "params": {...}}
   ```

4. **Используйте приоритеты** правильно:
   - 100-200: Обычные баффы
   - 200-300: Важные баффы (промо, подписки)
   - 300+: Критичные баффы (постоянные улучшения)

5. **Мониторьте истечение** через `buffs.list_active_buffs`:
   - Проверяйте `expires_at` для активных баффов
   - Автоматически продлевайте подписки через `buffs.extend_buff`

---

## Дополнительные примеры

### Пример: Триал с автоматическим продлением

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 123,
      "entity_kind": "user",
      "name": "Extended Trial",
      "type": "trial",
      "duration_days": 7,
      "effects": {"data.limits.api_calls": 10000},
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
  },
  {
    "tool": "buffs.extend_buff",
    "params": {
      "buff_id": "<buff_id>",
      "entity_id": 123,
      "entity_kind": "user",
      "additional_days": 7
    }
  }
]
```

### Пример: Групповое применение промо

Для применения промо к нескольким пользователям:

```json
[
  {
    "tool": "buffs.create_buff",
    "params": {
      "entity_id": 1,
      "entity_kind": "project",
      "name": "Group Promo",
      "type": "promo",
      "duration_days": 30,
      "effects": {"data.limits.api_calls": 50000}
    }
  },
  {
    "tool": "buffs.apply_buff",
    "params": {
      "buff_id": "<promo_buff_id>",
      "entity_id": 1,
      "entity_kind": "project"
    }
  }
]
```

Промо-баффы на проекте автоматически влияют на всех пользователей проекта.
