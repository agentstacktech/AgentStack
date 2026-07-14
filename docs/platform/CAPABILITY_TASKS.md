# Platform Task Capabilities (PTC)

**Comfort tasks** are small, bounded automation units: each task declares a **domain**, a **comfortBudget** (maximum MCP actions, typically **≤3**), and an **executionMode** (`inline` or `split`).

PTC aligns **Compass playbooks**, **Discovery Hub** slots, and **MCP agents** on the same task IDs.

---

## Catalog source

MCP action **`guidance.list_capability_tasks`** returns the live catalog (`taskId`, `domain`, `comfortBudget`, `executionMode`).

```json
{
  "action": "guidance.list_capability_tasks",
  "params": {}
}
```

Example tasks (non-exhaustive):

| taskId | domain | comfortBudget |
|--------|--------|---------------|
| `storage.upload` | storage.file | 3 |
| `hosting.deploy_zip` | hosting.site | 3 |
| `hosting.publish` | hosting.site | 3 |
| `project_hosting.ladder_host` | hosting.project | 2 |
| `project_hosting.complete_sell` | hosting.project | 3 |

---

## Comfort budget rules

1. **≤3 actions** per task unless the catalog explicitly lists a higher budget (rare).
2. Prefer **read → mutate → verify** patterns (e.g. list board → move stage → get timeline).
3. **`split` mode** — UI may span multiple screens; agents should still respect the action count per invocation.
4. **`inline` mode** — safe for single-shot agent tool loops.

---

## Compass alignment

Compass playbooks reference PTC `taskId` values in playbook steps. When an agent receives a playbook hint:

1. Resolve `taskId` via `guidance.list_capability_tasks`.
2. Map steps to MCP actions from [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).
3. Stop when `comfortBudget` is exhausted; return control to the user for the next task.

---

## MCP discovery pair

| Action | Role |
|--------|------|
| `guidance.list_capability_tasks` | Task catalog |
| `discovery.get_platform_surfaces` | Route catalog |

Use both before building cross-domain automations — see [COMPASS_AND_DISCOVERY.md](COMPASS_AND_DISCOVERY.md).

---

## SDK

```typescript
import { capabilityTasks } from "@agentstack/sdk/capability-tasks";

const tasks = await capabilityTasks.list(); // wraps guidance.list_capability_tasks
const task = tasks.find((t) => t.taskId === "hosting.publish");
```

---

## Synergy recipe

[compass-capability-tasks.md](../explanation/synergies/compass-capability-tasks.md) — Compass + PTC + MCP in one loop.

**Next:** [COMPASS_AND_DISCOVERY.md](COMPASS_AND_DISCOVERY.md) · [explanation/synergies/SYNERGY_COOKBOOK.md](../explanation/synergies/SYNERGY_COOKBOOK.md)
