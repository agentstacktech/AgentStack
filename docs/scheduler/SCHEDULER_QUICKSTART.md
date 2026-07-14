# Scheduler — integration quickstart

Cron-style **scheduled tasks** execute Protein commands, MCP actions, or registered handlers on a timetable.

> **Correct action name:** `scheduler.create_task` — there is no `schedule_task` action.

---

## Create a cron task

### MCP

```json
{
  "action": "scheduler.create_task",
  "params": {
    "name": "Nightly CRM export",
    "cron": "0 2 * * *",
    "command": "crm.export_contact",
    "enabled": true
  }
}
```

| Field | Description |
|-------|-------------|
| `name` | Human-readable label |
| `cron` | Standard 5-field cron (`minute hour day month weekday`) |
| `command` | Command identifier, endpoint path, or MCP action name |
| `enabled` | Default `true` |

Returns `task_id` (UUID) for later management.

---

## List tasks

```json
{
  "action": "scheduler.list_tasks",
  "params": { "project_id": 42 }
}
```

---

## Execute immediately (ad hoc)

```json
{
  "action": "scheduler.execute_task",
  "params": {
    "task_id": "task_20251230_125227_681021"
  }
}
```

---

## Other scheduler actions

| Action | Purpose |
|--------|---------|
| `scheduler.get_task` | Fetch task config |
| `scheduler.update_task` | Change cron, command, enabled flag |
| `scheduler.cancel_task` | Cancel running execution |
| `scheduler.get_pool_tasks` | Worker pool queue |
| `scheduler.delete_pool_task` | Remove pooled work item |

Full list: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) § scheduler.

---

## Cron examples

| Expression | Schedule |
|------------|----------|
| `0 0 * * *` | Daily at midnight UTC |
| `0 9 * * 1` | Mondays 09:00 |
| `*/15 * * * *` | Every 15 minutes |
| `0 * * * *` | Hourly |

---

## Synergy

Pair with Logic and webhooks — [webhook-logic-scheduler.md](../explanation/synergies/webhook-logic-scheduler.md).

**Next:** [MCP_QUICKSTART.md](../MCP_QUICKSTART.md) · [logic/LOGIC_ENGINE_V2.md](../logic/LOGIC_ENGINE_V2.md)
