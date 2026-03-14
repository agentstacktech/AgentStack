# MCP Buffs - Temporary effects

Examples of using MCP tools to apply temporary effects (trials, promos, bonuses).

## Contents

1. [Trial periods](#trial-periods)
2. [Promo campaigns](#promo-campaigns)
3. [Temporary bonuses](#temporary-bonuses)
4. [Workflows](#workflows)
5. [Error handling](#error-handling)

---

## Trial periods

### Example 1: 7-day trial for new user

**Scenario:** Grant a new user a 7-day trial with increased limits.

**Step 1: Create buff**
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

**Response:**
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

**Step 2: Apply buff**
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

**Response:**
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

**Step 3: List active buffs**
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

**Step 4: Get effective limits**
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

### Example 2: Quick trial apply (single step)

Use `buffs.apply_temporary_effect` for a one-step apply:

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

## Promo campaigns

### Example 1: Black Friday - project promo

**Scenario:** Apply a promo buff to the whole project for 30 days.

**Step 1: Create promo buff**
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

**Step 2: Apply to project**
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

### Example 2: Seasonal promo

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

## Temporary bonuses

### Example 1: Weekend bonus

**Scenario:** Grant the user a weekend bonus (2 days).

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

### Example 2: Event bonus

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

### Workflow 1: Full trial cycle

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

### Workflow 2: Trial extension

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

### Workflow 3: Apply promo to project

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

## Error handling

### Error: Buff not found

**Cause:** Buff does not exist or was deleted.

**Solution:**
```json
{
  "tool": "buffs.list_active_buffs",
  "params": {
    "entity_id": 123,
    "entity_kind": "user"
  }
}
```
Check the active buffs list to find the correct buff_id.

### Error: Buff not in PENDING state

**Cause:** Attempting to apply a buff that is already active or in another state.

**Solution:**
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
Check buff state before applying.

### Error: Entity not found

**Cause:** User or project does not exist.

**Solution:** Ensure entity_id and entity_kind are correct. For users, ensure project_id is set.

---

## Best Practices

1. **Always check buff state** before operations:
   ```json
   {"tool": "buffs.get_buff", "params": {...}}
   ```

2. **Use categories** to organize buffs:
   - "subscription" - for subscriptions
   - "event" - for promos
   - "trial" - for trials

3. **Check effective limits** after applying:
   ```json
   {"tool": "buffs.get_effective_limits", "params": {...}}
   ```

4. **Use priorities** correctly:
   - 100-200: Regular buffs
   - 200-300: Important buffs (promos, subscriptions)
   - 300+: Critical buffs (permanent upgrades)

5. **Monitor expiry** via `buffs.list_active_buffs`:
   - Check `expires_at` for active buffs
   - Renew subscriptions via `buffs.extend_buff`

---

## Additional examples

### Example: Trial with auto-renewal

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

### Example: Group promo apply

To apply a promo to multiple users:

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

Project-level promo buffs automatically apply to all project users.
