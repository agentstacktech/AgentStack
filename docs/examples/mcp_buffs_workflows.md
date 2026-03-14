# MCP Buffs - Workflows and tool combinations

Ready-made workflows and MCP tool combinations for managing buffs.

## Contents

1. [Basic workflows](#basic-workflows)
2. [Advanced workflows](#advanced-workflows)
3. [Tool combinations](#tool-combinations)
4. [Real-world scenarios](#real-world-scenarios)
5. [Best Practices](#best-practices)

---

## Basic workflows

### Workflow 1: Full trial cycle

**Description:** Create, apply and monitor a trial buff.

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

### Workflow 2: Extend subscription

**Description:** Find active subscription and extend it.

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

### Workflow 3: Cancel erroneous buff

**Description:** Check, revert and remove an erroneous buff.

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

### Workflow 4: Create and apply promo

**Description:** Create a promo buff for the project and verify effects.

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

## Advanced workflows

### Workflow 5: Convert trial to subscription

**Description:** After trial expiry, create and apply a subscription.

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

### Workflow 6: Apply multiple buffs

**Description:** Create and apply multiple buffs for combined effect.

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

### Workflow 7: Monitor and manage buffs

**Description:** Full cycle of monitoring active buffs and managing them.

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

## Tool combinations

### Combination 1: Create + Apply = Full apply cycle

**Use when:** You need to create a template and apply it immediately.


```json
[
  {"tool": "buffs.create_buff", "params": {...}},
  {"tool": "buffs.apply_buff", "params": {"buff_id": "<from previous>"}}
]
```

**Alternative:** Use `buffs.apply_temporary_effect` or `buffs.apply_persistent_effect` for a single step.

### Combination 2: List + Extend = Extend subscription

**Use when:** Find active subscription and extend it.

```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.extend_buff", "params": {"buff_id": "<from previous>"}}
]
```

### Combination 3: Get + Revert + Cancel = Full cancellation

**Use when:** Check state, revert effects and remove buff.

```json
[
  {"tool": "buffs.get_buff", "params": {...}},
  {"tool": "buffs.revert_buff", "params": {...}},
  {"tool": "buffs.cancel_buff", "params": {...}}
]
```

**Note:** For ACTIVE buffs `buffs.cancel_buff` automatically performs revert before removal.

### Combination 4: Apply + Get Effective Limits = Verify results

**Use when:** Apply buff and immediately verify limit changes.

```json
[
  {"tool": "buffs.apply_buff", "params": {...}},
  {"tool": "buffs.get_effective_limits", "params": {...}}
]
```

### Combination 5: List + Get = Detailed analysis

**Use when:** Get list of active buffs and detailed info for each.

```json
[
  {"tool": "buffs.list_active_buffs", "params": {...}},
  {"tool": "buffs.get_buff", "params": {"buff_id": "<each_buff_id>"}}
]
```

---

## Real-world scenarios

### Scenario 1: SaaS subscription with auto-renewal

**Task:** Build a subscription system with automatic renewal via scheduler.

**Step 1: Create subscription**
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

**Step 2: Apply**
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

**Step 3: Configure auto-renewal (via scheduler)**
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

### Scenario 2: Promo campaign for user group

**Task:** Apply promo buff to the whole project (affects all users).

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

### Scenario 3: Batch purchase of upgrades

**Task:** Apply multiple permanent upgrades for cumulative effect.

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

Total limit: 200,000 API calls (sum of all buffs).

### Scenario 4: Cancel erroneous buff with verification

**Task:** Safely cancel an active buff with revert capability check.

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

If revert fails (insufficient resources), an error with details will be returned.

---

## Best Practices

### 1. Always check state before operations

```json
{"tool": "buffs.get_buff", "params": {...}}
```

### 2. Use categories for organization

- `"subscription"` - for subscriptions
- `"event"` - for promos
- `"trial"` - for trials
- `"permanent"` - for permanent upgrades

### 3. Set priorities correctly

- 100-200: Regular buffs
- 200-300: Important buffs (promos, subscriptions)
- 300+: Critical buffs (permanent upgrades)

### 4. Check effective limits after changes

```json
{"tool": "buffs.get_effective_limits", "params": {...}}
```

### 5. Monitor buff expiry

Regularly check `expires_at` via `buffs.list_active_buffs` and auto-renew subscriptions.

### 6. Use quick methods for simple cases

- `buffs.apply_temporary_effect` - for one-off temporary effects
- `buffs.apply_persistent_effect` - for permanent upgrades
- `buffs.create_buff + buffs.apply_buff` - when you need a reusable template

### 7. Handle errors gracefully

- Check `buff_not_found` - use `buffs.list_active_buffs` to find
- Check `insufficient_permissions` - admin rights required for ACTIVE buffs
- Check `revert_failed` - resources cannot be reverted

---

## Integration with other tools

### Scheduler integration

Auto-renew subscriptions:

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

### Logic Engine integration

Auto-apply buffs on events:

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

## Common errors and solutions

### Error: "Revert failed: insufficient resources"

**Cause:** Buff added resources that were spent; revert is not possible.

**Solution:**
- Check current state via `buffs.get_buff`
- Use `buffs.cancel_buff` with admin rights for forced cancellation
- Or wait for buff expiry (automatic revert)

### Error: "Buff does not support extension"

**Cause:** `config.extends_on_reapply` = false.

**Solution:** Create a new buff instead of extending the existing one.

### Error: "Insufficient permissions to cancel active buff"

**Cause:** Attempt to cancel ACTIVE buff without admin/owner rights.

**Solution:**
- For PENDING buffs - normal rights are enough
- For ACTIVE buffs - admin/owner rights required
- Use `buffs.revert_buff` separately if you don't have cancel rights

---

## Advanced techniques

### Technique 1: Cumulative effects

Applying multiple buffs to sum effects:

```json
[
  {"tool": "buffs.apply_persistent_effect", "params": {"effects": {"data.limits.api_calls": 100000}}},
  {"tool": "buffs.apply_persistent_effect", "params": {"effects": {"data.limits.api_calls": 100000}}},
  {"tool": "buffs.get_effective_limits", "params": {...}}
]
```

Total limit will be the sum of all buffs.

### Technique 2: Conditional apply

Check existing buffs before applying:

```json
[
  {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}},
  {"tool": "buffs.create_buff", "params": {...}},  // Only if no active subscription
  {"tool": "buffs.apply_buff", "params": {...}}
]
```

### Technique 3: Group management

Applying buffs to multiple users:

```json
[
  {"tool": "projects.get_users", "params": {"project_id": 1}},
  {"tool": "buffs.apply_temporary_effect", "params": {"entity_id": "<each_user_id>", ...}}
]
```

---

**Last updated:** 2025-01-20
**Version:** 1.0.0
