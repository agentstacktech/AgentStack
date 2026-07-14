# Logic Engine v2

The **Logic Engine** is AgentStack’s visual automation canvas: triggers, processors, and MCP action nodes. **v2** adds simulation, version history, and catalog discovery over MCP.

---

## UI

Developer shell → project → **Logic** — canvas editor with **Simulate** panel and **Version history** drawer.

---

## Core MCP (`logic.*`)

| Action | Purpose |
|--------|---------|
| `logic.create` | New rule |
| `logic.update` | Replace rule graph |
| `logic.get` | Rule detail |
| `logic.list` | All project rules |
| `logic.execute` | Run rule now (side effects) |
| `logic.delete` | Remove rule |

Requires `logic_write` cap for mutations.

---

## v2 surfaces

| Action | Purpose |
|--------|---------|
| `logic.dry_run` | **Simulate** without side effects |
| `logic.list_versions` | Version history snapshots |
| `logic.diff_versions` | Compare two snapshots |
| `logic.restore_version` | Roll back to snapshot |
| `logic.export_json` | Backup / review JSON |
| `logic.import_json` | Import rule from JSON |
| `logic.install_blueprint` | Install catalog blueprint |
| `logic.attach_template` | Attach template from catalog |
| `logic.signals_catalog` | Available trigger signals |
| `logic.mcp_actions_catalog` | MCP actions addressable from nodes |

---

## Simulate (dry run)

```json
{
  "action": "logic.dry_run",
  "params": {
    "project_id": 42,
    "logic_id": "rule_abc123",
    "command_data": { "event": "order.paid", "order_id": "ord_1" },
    "mocks": {},
    "pin_data": {}
  }
}
```

Returns step `trace` and final `protein` state — no writes.

---

## Version history

```json
{
  "action": "logic.list_versions",
  "params": { "project_id": 42, "logic_id": "rule_abc123" }
}
```

```json
{
  "action": "logic.restore_version",
  "params": {
    "project_id": 42,
    "logic_id": "rule_abc123",
    "version_id": "v3"
  }
}
```

---

## Integration Hub

Recipes install declarative logic via `integrations.install_recipe` — see [integrations/INTEGRATION_QUICKSTART.md](../integrations/INTEGRATION_QUICKSTART.md).

**Synergy:** [webhook-logic-scheduler.md](../explanation/synergies/webhook-logic-scheduler.md)

**Next:** [api/logic.md](../api/logic.md) · [scheduler/SCHEDULER_QUICKSTART.md](../scheduler/SCHEDULER_QUICKSTART.md)
