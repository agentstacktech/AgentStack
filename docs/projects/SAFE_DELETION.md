# Safe project deletion

Deleting a project is **irreversible** after execution. AgentStack provides a **dismantling wizard** in the dashboard and matching REST/MCP APIs so owners see **blockers** before anything is destroyed.

---

## Dashboard flow

1. Open project **Settings** → **Danger zone**.
2. Run **Deletion inventory** — lists blockers (active subscriptions, hosted traffic, pending commerce, team members, …) and warnings.
3. Resolve **blockers** (or accept that deletion remains blocked).
4. **Schedule** deletion (cooling-off window) or **execute** when inventory is clear.
5. **Cancel** during the window if you change your mind.

The UI does not expose internal orchestration tokens to end users — you receive a short-lived **execution token** only when the inventory allows proceed.

---

## REST API

Prefix: **`/api/projects/{project_id}`**

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/deletion-inventory` | Blockers, warnings, eligibility |
| `POST` | `/deletion-schedule` | Schedule after inventory pass |
| `POST` | `/deletion-cancel` | Cancel scheduled deletion |
| `POST` | `/deletion-execute` | Execute with execution token + optional `Idempotency-Key` |
| `GET` | `/deletion-status` | Current schedule / run status |

Requires authentication and API key cap **`project_admin`**.

### Inventory example

```bash
curl -sS "https://agentstack.tech/api/projects/42/deletion-inventory?refresh=true" \
  -H "Authorization: Bearer $TOKEN"
```

Response includes `blockers[]`, `warnings[]`, and whether deletion may proceed.

### Execute example

```bash
curl -sS -X POST "https://agentstack.tech/api/projects/42/deletion-execute" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: delete-42-once" \
  -d '{ "execution_token": "TOKEN_FROM_INVENTORY" }'
```

---

## MCP

| Action | Purpose |
|--------|---------|
| `projects.get_deletion_inventory` | Same as GET inventory |
| `projects.schedule_deletion` | Schedule with execution token |
| `projects.execute_deletion` | Final delete |
| `projects.delete_project` | Legacy path when inventory has zero blockers |

```json
{
  "action": "projects.get_deletion_inventory",
  "params": { "project_id": 42, "refresh": true }
}
```

---

## Blockers (typical)

| Blocker class | Resolution |
|---------------|------------|
| Active paid subscription | Cancel or downgrade billing |
| Published sites with traffic policy | Unpublish or migrate |
| Open commerce escrows | Settle or cancel deals |
| Shared team ownership | Transfer or remove members |

Warnings do not always block — read inventory messages.

---

## Caveats

- Execution is **async fan-out** across tissues (CRM, hosting, wallets, …) — status endpoint may show `in_progress`.
- Idempotency-Key prevents double-delete on retries.
- Ecosystem template project (`id=1`) is never deletable via this API.

**Next:** [subscription/SUBSCRIPTION_TIERS.md](../subscription/SUBSCRIPTION_TIERS.md) · [auth/API_KEYS_AND_SCOPES.md](../auth/API_KEYS_AND_SCOPES.md)
