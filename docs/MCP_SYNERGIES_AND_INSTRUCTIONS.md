# MCP synergies and instructions

How domains fit together and recommended operation order for agents. **Step-by-step flows:** [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) · **Action list:** [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md)

---

## 1. Coverage overview

### Fully covered (with permissions)

| Domain | Examples |
|--------|----------|
| Projects | `projects.get_project`, `create_project`, `get_users`, … |
| Scheduler | `scheduler.create_task`, `list_tasks`, `execute_task`, … |
| Assets | `assets.create`, `assets.list` (currencies: `type: currency`) |
| Buffs | `buffs.create_buff`, `apply_buff`, `get_effective_limits`, … |
| Logic | `logic.create`, `logic.execute`, `get_processors`, … |
| Wallets / payments | `wallets.*`, `payments.*` |
| Data | `commands.execute` (dna_crud) |
| Auth | `auth.login`, `auth.register`, … |
| Analytics | `analytics.get_usage`, `get_metrics` |

### Partial or REST-only

| Area | Note |
|------|------|
| Webhooks | Use [integrations/](integrations/README.md) hub + Logic; MCP `integrations.*` for recipes |
| Some notification sends | Prefer Logic processors or documented REST |

---

## 2. Synergies (combine tools)

- **Project → economy:** `create_project_anonymous` → `assets.create` (currency) → `wallets.create` → `wallets.deposit`
- **Project → members:** `get_users` → `add_user` / `update_user_role` (Professional tier for add/remove where required)
- **Trial / subscription:** `buffs.create_buff` → `logic.create` (trigger) → `apply_buff` / `apply_temporary_effect`
- **Payment → entitlement:** `payments.create` → `buffs.apply_buff` or `apply_persistent_effect`
- **Schedule + logic:** `scheduler.create_task` → payload calls `logic.execute`
- **Limits:** `analytics.get_usage` + `logic.create` + temporary buffs
- **Universal data:** `commands.execute` on DNA entities for app-specific JSON

**Paradigms:** buffs behave like timed hooks; projects hold global `project.data`; users hold `user.data` rows.

---

## 3. Recommended order

### New project with economy

1. Create project (save `project_id`, API key)  
2. Create currency assets  
3. Create wallets and deposit  
4. Optional: logic rules and buff templates  

### SaaS trial

1. Project + trial buff template  
2. Logic on `user.created` → apply trial buff  
3. Payment → revert trial → apply subscription buff  
4. Scheduler for expiry checks  

### Game rewards

1. Currencies as assets  
2. Wallets or `user.data` via commands  
3. Buffs for rewards  
4. Scheduler for daily grants  

---

## 4. Agent routing

Use [plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md) to pick domains. Validate sequences with `GET /mcp/recipes` when available.

---

**See also:** [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) · [MCP_QUICKSTART.md](MCP_QUICKSTART.md) · [plugins/CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md)
