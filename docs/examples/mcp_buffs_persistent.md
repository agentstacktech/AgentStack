# MCP Buffs - Persistent effects

Examples of using MCP tools to apply persistent effects (subscriptions, one-time purchases, upgrade packs).

## Contents

1. [Subscriptions](#subscriptions)
2. [One-time purchases](#one-time-purchases)
3. [Upgrade packs](#upgrade-packs)
4. [Workflows](#workflows)
5. [Error handling](#error-handling)

---

## Subscriptions

### Example 1: Premium monthly subscription

**Scenario:** Create and apply a monthly subscription with auto-renewal.

**Step 1: Create subscription**
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

**Step 2: Apply subscription**
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

**Step 3: Extend subscription (after one month)**
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

### Example 2: Annual subscription

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

## One-time purchases

### Example 1: Lifetime Premium

**Scenario:** Apply a permanent upgrade that never expires.

**Option 1: Via create + apply**
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

**Option 2: Quick apply (single step)**
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

### Example 2: Extra limits pack

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

## Upgrade packs

### Example 1: Starter pack

**Scenario:** Apply a set of permanent upgrades for a new user.

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

### Example 2: Business pack

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

### Workflow 1: Create and apply subscription

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

### Workflow 2: Extend subscription

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

### Workflow 3: Apply permanent upgrade

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

### Workflow 4: Cancel permanent buff (admin rights required)

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

**Note:** For permanent buffs in ACTIVE state, admin/owner rights are required. Cancel will automatically perform revert before removal.

---

## Error handling

### Error: Persistent buffs cannot be reverted

**Cause:** Attempt to revert a permanent buff.

**Solution:** Permanent buffs are not reverted. Use `buffs.cancel_buff` to remove (admin rights required).

### Error: Insufficient permissions to cancel active buff

**Cause:** Attempt to cancel an active buff without admin/owner rights.

**Solution:**
- For PENDING buffs - normal rights are enough
- For ACTIVE buffs - admin/owner rights required
- Check rights via `projects.get_project` or use another approach

### Error: Buff does not support extension

**Cause:** Attempt to extend a buff that does not support extension.

**Solution:** Check `config.extends_on_reapply` via `buffs.get_buff`. If false, create a new buff instead of extending.

---

## Best Practices

1. **Use persistent=true** for permanent effects:
   ```json
   {"config": {"persistent": true, "revert_on_expire": false}}
   ```

2. **Set high priority** for permanent buffs:
   - 300+ for lifetime purchases
   - 250+ for annual subscriptions
   - 200+ for monthly subscriptions

3. **Monitor subscriptions** via regular checks:
   ```json
   {"tool": "buffs.list_active_buffs", "params": {"category": "subscription"}}
   ```

4. **Automate renewal** via scheduler:
   - Use `scheduler.schedule_task` for automatic renewal
   - Check `expires_at` before expiry

5. **Combine buffs** for cumulative effects:
   - Multiple permanent buffs stack
   - Use `buffs.get_effective_limits` to verify total limits

---

## Additional examples

### Example: Cumulative packs

Applying multiple permanent buffs for cumulative effect:

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

The final limit will be the sum of all buffs.

### Example: Subscription with auto-renewal

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

Use `scheduler.schedule_task` to auto-renew via `buffs.extend_buff` every 30 days.
