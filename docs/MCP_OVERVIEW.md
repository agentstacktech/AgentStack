# MCP — Overview and Architecture

- [Overview](#overview)
- [Architecture](#architecture)
- [API Endpoints](#api-endpoints)

**Other sections:** [Quick start](MCP_QUICKSTART.md) · [Tools reference](MCP_TOOLS.md) · [Features and examples](MCP_FEATURES_EXAMPLES.md) · [Capabilities and metrics](MCP_CAPABILITY_MAP.md) · [Synergies and instructions](MCP_SYNERGIES_AND_INSTRUCTIONS.md) · [Flows and synergies (full steps)](MCP_FLOWS_AND_SYNERGIES.md)

---

## Overview

**MCP (Model Context Protocol)** is a universal server for managing all AgentStack services through a single interface. MCP gives AI agents (Google Studio, Cursor, VS Code, and others) full access to the platform via a standardized set of tools.

### Main characteristics

- **Protocol version:** 2.0
- **Model:** 1 tool (`agentstack.execute`); 62+ actions for steps — see GET /mcp/actions
- **Streaming support:** Yes
- **Idempotency:** Yes
- **Job tracking:** Yes
- **Port:** 8000 (via API Gateway)

### Key capabilities

- ✅ **Full management** — with proper permissions: full control over users, data, projects, scheduler, assets, buffs (CRUD and domain operations)
- ✅ Full project management (create, read, update, delete; add/update/remove users; anonymous creation)
- ✅ RBAC: rbac.get_roles, rbac.assign_role, rbac.revoke_role, rbac.check_permission; projects.update_user_role for role assignment
- ✅ Authentication and authorization
- ✅ Payment management
- ✅ Task scheduler (create, get, update, list, cancel, execute, pool operations)
- ✅ Analytics and metrics
- ✅ API key management (create, list, delete)
- ✅ Rules Engine / Logic (create, update, delete, get, list, execute)
- ✅ Assets (create, get, list, update, delete)
- ✅ Buff system (create, apply, extend, revert, cancel, get, list, limits, temporary/persistent effects)
- ✅ Webhooks, Notifications, Wallets

---

## Architecture

### Project structure

```
mcp/
├── main.py              # FastAPI app, middleware, registration
├── routes.py            # API endpoints for tools
├── tools.py             # Implementation of all MCP tools
├── sdk_wrapper.py       # SDK wrapper for project operations
└── dependencies.py      # Dependencies
```

### Components

1. **FastAPI Application** (`main.py`)
   - CORS middleware
   - Metrics middleware
   - Correlation ID tracking
   - Health checks
   - Prometheus metrics

2. **Routes** (`routes.py`)
   - `GET /mcp/discovery` — discovery (single tool agentstack.execute)
   - `GET /mcp/actions` — list all actions by domain
   - `POST /mcp` — execute batch of steps (agentstack.execute)
   - `POST /mcp/tools` — JSON-RPC tools/call compatibility
   - `POST /mcp/stream` — streaming execution
   - `GET /mcp/jobs/{job_id}` — job status
   - OAuth, AI prompts, recipes, health, cache/clear under `/mcp`

3. **Tools Registry** (`tools.py`)
   - All tools registered in `MCP_TOOLS`
   - Pydantic models for request validation
   - Error handling and logging

4. **SDK Wrapper** (`sdk_wrapper.py`)
   - Wrapper over HTTP API for project operations
   - Uses existing endpoints from `agentstack-core`
   - Unified interface for all operations

---

## Execute and discovery

A single tool **agentstack.execute** with batched steps; suitable for providers that limit the number of tools. One API, async jobs and streaming.

- **Base URL:** `https://agentstack.tech/mcp`
- **Execute (sync):** `POST /mcp` — body: `{ "steps": [ { "id": "p1", "action": "projects.create_project_anonymous", "params": { "name": "My app" } } ], "options": { "stopOnError": true } }`
- **Execute (async job):** `POST /mcp` with `"options": { "async": true, "idempotency_key": "..." }` → returns `job_id`; status via `GET /mcp/jobs/{job_id}`.
- **Execute (stream):** `POST /mcp/stream` — same body, response is `text/event-stream` with `started` and `completed` events.
- **List actions:** `GET /mcp/actions` — all valid `action` values by domain (projects, buffs, auth, payments, logic, assets, scheduler, analytics, etc.).
- **Discovery:** `GET /mcp/discovery` — protocol, single-tool schema, streaming and jobs flags.
- **Health:** `GET /mcp/health` — lightweight health check.
- **AI help:** `POST /mcp/ai/plan_steps` — suggests `steps[]` from goal and history.

Steps can reference previous results via `{ "from": "stepId.result.field" }` and use optional `if` for conditions. See [CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md) and [MCP_CAPABILITY_MAP.md](MCP_CAPABILITY_MAP.md).

### Full management and permissions

MCP provides **full management** across all main domains when the caller has the required permissions:

| Domain | Read | Create | Update | Delete / Cancel | Permissions |
|--------|------|--------|--------|-----------------|-------------|
| **Projects** | get_projects, get_project, get_stats, get_users | create_project, create_project_anonymous | update_project | delete_project | owner / ecosystem |
| **Project members** | get_users | add_user | update_user_role | remove_user | owner or manage_users; add/remove — Professional |
| **RBAC** | rbac.get_roles, rbac.check_permission | — | rbac.assign_role (or projects.update_user_role) | rbac.revoke_role | manage_users / owner; owner cannot be assigned via rbac.assign_role |
| **Scheduler** | get_task, list_tasks, get_pool_tasks, get_pool_task_details | create_task | update_task | cancel_task, delete_pool_task, clear_pool_tasks | write on scheduler |
| **Assets** | get, list | create | update | delete | project + authentication |
| **Buffs** | get_buff, list_active_buffs, get_effective_limits | create_buff | extend_buff | revert_buff, cancel_buff | project + authentication |
| **Logic** | get, list, get_processors, get_commands | create | update | delete | project + authentication |
| **Data (generic)** | — | — | — | — | commands.execute (dna_crud: get, create, update, delete on entities) |
| **Project currencies** | assets.get, assets.list | assets.create (type: currency) | assets.update | assets.delete | Currencies = assets with type=currency; full CRUD via assets.* |
| **Wallets** | wallets.list, payments.get_balance | wallets.create | — | — | wallets.deposit, wallets.transfer for deposits and transfers |

The `context: { "project_id", "user_id" }` in the request body overrides the session for the whole batch; for the ecosystem (project_id=1) management operations on projects and users are available without Professional subscription checks.

### Recommended call combinations

- **Profile + projects:** 1) `resources/read` `agentstack://me` 2) `tools/call` with `agentstack.execute` and one step `projects.get_projects` (or a batch with refs).
- **Project context:** `POST /mcp` with `context: { "project_id": N }` and steps; or `tools/call` with `arguments: { "steps": [...], "context": { "project_id": N } }` (context overrides session for the batch).
- **Chained steps:** One batch: step 1 `projects.get_projects`, step 2 `projects.get_users` with `params: { "project_id": {"from": "s1.result.projects[0].id"} }`.
- **Buffs:** Batch `buffs.create_buff` then `buffs.apply_buff` with `buff_id: {"from": "create_step.result.buff_id"}`; use response `applied_buffs_summary` for who got what.
- **Planning:** `POST /mcp/ai/plan_steps` with goal (and optional history) then execute returned `steps[]` via `POST /mcp` or `tools/call` agentstack.execute.

---

## API Endpoints

**Public endpoints (no X-API-Key):** Calls without the auth header are allowed for `GET /mcp/tools` (tool list) and for the `projects.create_project_anonymous` tool (create anonymous project and get keys). All other requests require `X-API-Key`.

### Discovery and metadata

#### `GET /mcp/tools`
Returns the list of all available tools, grouped by category. **Can be called without X-API-Key.**

**Response:**
```json
{
  "tools": {
    "auth": [...],
    "payments": [...],
    "projects": [...],
    ...
  },
  "version": "2.0",
  "total_tools": 60,
  "description": "Full AgentStack MCP integration..."
}
```

#### `GET /mcp/discovery`
Discovery endpoint for automatic capability detection.

**Response:**
```json
{
  "protocol_version": "2.0",
  "capabilities": {
    "streaming": true,
    "idempotency": true,
    "job_tracking": true
  },
  "services": {
    "auth": {...},
    "projects": {...},
    ...
  }
}
```

### Tool execution

#### `POST /mcp` (primary)
Single tool **agentstack.execute**: send a batch of steps. Each step has `id`, `action` (e.g. `projects.get_projects`, `buffs.apply_buff` — full list at GET /mcp/actions), `params`. Optional `context`: `{ "project_id", "user_id" }` overrides session for all steps.

**Request:**
```json
{
  "steps": [
    { "id": "p1", "action": "projects.get_projects", "params": {} },
    { "id": "create", "action": "projects.create_project_anonymous", "params": { "name": "My Project" } }
  ],
  "context": { "project_id": 123 }
}
```

**Response:** `{ "success", "steps": [...], "applied_buffs_summary" (if any buff apply step succeeded) }`

#### `POST /mcp/tools/{tool_name}` (compatibility)
Executes one action by name (for clients that call tools individually). Prefer POST /mcp with steps for batching and context override.

#### `POST /mcp/tools/{tool_name}/stream`
Executes the tool with a streaming response (Server-Sent Events).

**Response:** SSE stream with events:
- `status: started`
- `status: processing`
- `status: completed`
- `status: error`

#### `GET /mcp/jobs/{job_id}`
Returns the status of an async job.

### Prompts and caching

- **`prompts/list`** — Returns available prompts. Some clients (e.g. Cursor) call this repeatedly (e.g. on connect and when refreshing). The server caches the list for 60 seconds so repeated calls within that window do not recompute it.

---

## Troubleshooting

### POST /mcp returns 500 Internal Server Error (tools/call)

**Symptom:** Client (e.g. Cursor MCP) reports "Streamable HTTP error: Internal Server Error" when calling a tool (e.g. `projects.get_projects`).

**Cause:** Tool result contained non-JSON-serializable values (e.g. `uuid.UUID`, `datetime`). Responses from `tools/call` are now serialized via a single mechanism (`serialize_for_json`) before `json.dumps`, so UUID and datetime are converted to strings.

**What to do:**

1. Check backend logs for the exact exception, e.g. `TypeError: Object of type UUID is not JSON serializable` near `json.dumps` or `_handle_jsonrpc_request`.
2. Ensure the deployed backend includes the fix: in `_handle_jsonrpc_request` the `tools/call` branch uses `serialize_for_json(result_payload)` before `json.dumps`. Redeploy if needed.
3. For other 500s on POST /mcp: search logs for `Exception in ASGI application` and the traceback; fix the failing path (tool implementation or route) and add error handling so tool errors return 200 with `result.isError: true` instead of 500 where appropriate. See [deploy/vps/RUNBOOK_MCP_500.md](../deploy/vps/RUNBOOK_MCP_500.md) for runbook steps.
