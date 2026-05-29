# MCP flows and synergies

Reference for MCP transports, connection flows, resource subscriptions, and multi-step tool chains. **Catalog:** [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md) · **Scale:** [MCP_SCALE.md](MCP_SCALE.md)

---

## 1. Transports and endpoints

| Transport | Endpoint | Purpose |
|-----------|----------|---------|
| **JSON-RPC 2.0** | `POST /mcp` (`method`, `params`, `id`) | initialize, tools/list, tools/call, prompts/*, resources/* |
| **REST** | `GET /mcp/discovery` | One tool `agentstack.execute`, `actions_url` |
| **REST** | `GET /mcp/actions` | All action ids by domain |
| **REST** | `POST /mcp` | Batch execute (`steps[]`, `context`, `options`) |
| **REST** | `POST /mcp/stream` | SSE streaming execution |
| **REST** | `GET /mcp/jobs/{job_id}` | Async job status (`options.async: true`) |
| **SSE** | `GET /mcp/resources/notifications` | Resource change notifications |
| **REST** | `GET /mcp/health` | Health + discovery pointers |

---

## 2. JSON-RPC methods (`POST /mcp`)

| method | Description |
|--------|-------------|
| `initialize` | MCP handshake |
| `tools/list` | Returns `agentstack.execute` + `actions_url` |
| `tools/call` | Call tool; for `agentstack.execute` pass `steps` — **response is first step only** |
| `prompts/list`, `prompts/get` | Cached prompt templates |
| `resources/list`, `resources/read`, `resources/subscribe`, `resources/unsubscribe` | URI resources |

**Full batch results:** use REST `POST /mcp` with `{ "steps": [...] }`, not `tools/call` alone.

---

## 3. First connection flow

```
1. POST /mcp  →  initialize
2. GET /mcp/actions  (optional early)
3. POST /mcp  →  tools/list
4. POST /mcp  →  { "steps": [ { "id": "s1", "action": "projects.get_project", "params": {...} } ], "options": { "stopOnError": true } }
```

Use `context.project_id` / `user_id` when steps need implicit scope.

---

## 4. Resources (list → subscribe → SSE → read)

Allowed URIs include `agentstack://me`, `agentstack://projects/{id}`, `agentstack://projects/{id}/users/{user_id}`.

1. Open `GET /mcp/resources/notifications` (SSE) — save `connection_id`
2. `resources/subscribe` with `uri` + `connection_id`
3. On `notifications/resources/updated`, call `resources/read` for fresh JSON
4. `resources/unsubscribe` when done

---

## 5. Prompts + tools

1. `prompts/list` → `prompts/get(name)`
2. Execute template steps via `agentstack.execute` (e.g. `logic.get_processors` → `logic.create`)

---

## 6. Synergy chains (examples)

### Project + economy

| Step | action | Notes |
|------|--------|-------|
| 1 | `projects.create_project_anonymous` | Get `project_id`, API key |
| 2–3 | `assets.create` | Currencies |
| 4–5 | `wallets.create`, `wallets.deposit` | Balances |

### Project + RBAC

`projects.get_project` → `projects.get_users` → `rbac.get_roles` → `rbac.assign_role`

### Trial / SaaS

`buffs.create_buff` → `logic.create` (trigger) → `buffs.apply_buff` → payments / scheduler as needed

### Resources + mutations

SSE subscribe → `projects.update_project` → notification → `resources/read`

---

## 7. Named tool chains

| Chain | Actions (order) |
|-------|-----------------|
| user_with_trial | create_project_anonymous → create_buff → apply_buff |
| payment_to_subscription | payments.create → create_buff → apply_buff → scheduler.create_task |
| rbac_manage_members | get_project → get_users → get_roles → assign_role |

Recipes: `GET /mcp/recipes`, `POST /mcp/recipes/{id}/validate`

---

## 8. Dependencies (summary)

- `buffs.apply_buff` needs `buffs.create_buff` first
- `logic.execute` needs `logic.create`
- Scheduler and commands need project context
- Use `GET /mcp/actions` + [plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md) for routing

---

## 9. Agent checklist

1. Create project → save ids and API key  
2. Currencies → `assets.create`  
3. Wallets → `wallets.*`  
4. Trials → `buffs.*` + `logic.create`  
5. Cron → `scheduler.create_task`  
6. Members → `projects.get_users` + `rbac.*`  
7. Live project JSON → resources SSE + subscribe  
8. Bulk DNA → `commands.execute` / `sdk.protocol.executeCommand`

---

**See also:** [MCP_SYNERGIES_AND_INSTRUCTIONS.md](MCP_SYNERGIES_AND_INSTRUCTIONS.md) · [MCP_QUICKSTART.md](MCP_QUICKSTART.md) · [MCP_CAPABILITY_MAP.md](MCP_CAPABILITY_MAP.md)
