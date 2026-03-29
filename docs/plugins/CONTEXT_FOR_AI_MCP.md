# AgentStack MCP — Capability Map for AI

**Purpose:** MCP exposes one tool `agentstack.execute` at `https://agentstack.tech/mcp`. Use this map to choose the right **action** and step sequence for a user request. Full action list: `GET /mcp/actions`.

---

## Request shape (agentstack.execute)

```json
{
  "steps": [
    { "id": "step1", "action": "projects.get_projects", "params": {} },
    { "id": "step2", "action": "projects.get_project", "params": { "project_id": { "from": "step1.result.projects[0].id" } } }
  ],
  "options": { "stopOnError": true }
}
```

- **Reference previous step:** In `params`, use `{ "from": "stepId.result.path.to.field" }` (e.g. `"p1.result.project_id"`).
- **Reference context:** Use `{ "from": "context.project_id" }` for request context (project_id, user_id, etc.).
- **Conditional step:** Add `"if": { "equals": [ { "from": "pay.result.status" }, "success" ] }` to run a step only when the condition holds.

---

## User request → action (and typical steps)

| User request (example) | Domain | action(s) | Notes |
|------------------------|--------|-----------|--------|
| Create a project | Projects | `projects.create_project_anonymous` or `projects.create_project` | First step often; then use `p1.result.project_id` in later steps. |
| List my projects | Projects | `projects.get_projects` | params: `{}` or `{ "limit": 50 }`. |
| Get one project / project stats | Projects | `projects.get_project`, `projects.get_stats` | Need `project_id` (literal or from previous step). |
| Give user a 7-day trial | Buffs | `buffs.apply_temporary_effect` | Params: project_id, user_id, effect id/code, duration. |
| List active subscriptions / buffs | Buffs | `buffs.list_active_buffs`, `buffs.get_effective_limits` | project_id, optional user_id. |
| Create payment / check status / refund | Payments | `payments.create_payment`, `payments.get_status`, `payments.refund` | Chain with `if` on status for next step. |
| Login / register / get profile | Auth | `auth.quick_auth`, `auth.create_user`, `auth.get_profile`, `auth.update_profile` | Session/identity. |
| Create rule / when-then logic | Rules / Logic | `logic.create`, `rules.create_rules`, `rules.list_rules` | Automation, triggers. |
| Assets / inventory | Assets | `assets.create`, `assets.list` | project_id in params. |
| Schedule task / list tasks | Scheduler | `scheduler.schedule_task`, `scheduler.list_tasks`, `scheduler.cancel_task` | |
| Analytics / usage / budget | Analytics | `analytics.get_usage`, `analytics.set_budget`, `analytics.get_metrics` | |
| API keys (project) | API Keys | `projects.get_api_keys`, `projects.create_api_key`, `projects.delete_api_key` | |
| Webhooks / notifications | Other | `webhooks.create_endpoint`, `notifications.send_notification` | See full list in GET /mcp/actions. |

---

## Full action list

**GET /mcp/actions** returns all available `action` values grouped by domain. Use it to discover exact names (e.g. `buffs.apply_temporary_effect`, `projects.create_project_anonymous`) and short summaries.

---

## Related docs

- [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) — legacy multi-tool capability map.
- [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) — index.
- [MCP_OVERVIEW.md](../MCP_OVERVIEW.md) — API endpoints.

**Version:** 0.1 — for agentstack.execute. Update when new domains or actions are added; action list is authoritative at GET /mcp/actions.
