# Working with AgentStack Ecosystem Data

**See also:** [MCP and ecosystem — index](MCP_AND_ECOSYSTEM.md), [DNA Key-Value API](architecture/DNA_KEY_VALUE_API.md), [complex project examples](examples/mcp_complex_projects.md).

In the AgentStack ecosystem **there are no versions in API paths** (paths like `/api/...` are used). Storing and reading project and user data is implemented via existing endpoints and MCP tools — a separate "Ecosystem API" is not used.

---

## Where data handling is implemented

| Task | Endpoint or tool | Description |
|------|------------------|-------------|
| Project data (read/write by path) | `GET /api/projects/{project_id}/data`<br>`PATCH /api/projects/{project_id}/data` | Optional `?path=...` for GET; body `{ "path": "...", "value": ... }` for PATCH. Authentication and project access required. |
| Current user data in project | `GET /api/projects/{project_id}/users/me/data`<br>`PATCH /api/projects/{project_id}/users/me/data` | Same path/value format. Data is stored in `data_projects_user` for the project_id + user_id pair. |
| Key-value (project.data.* / user.data.*) | `GET /api/dna/data`<br>`POST /api/dna/data` | Query: `key` (required), `project_id` (for `project.data.*`). Body: `{ "key", "value", "project_id" }`. See [DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md). |
| Full project (including data) via MCP | `projects.get_project`, `projects.update_project` | `data` field in request/response. Convenient for agents and plugins. See [MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md). |

Base URL for cloud: **https://agentstack.tech**. Authentication: session (REST) or `X-API-Key` header (REST/MCP).

---

## Quick start

1. **Get an API key**
   - Via the [agentstack.tech](https://agentstack.tech) dashboard: create a project and obtain an API key.
   - Or without registration: a single MCP request — `projects.create_project_anonymous` with the project name; the response will include `user_api_key`, `project_id`. See [MCP_TOOLS.md](MCP_TOOLS.md) (Projects Tools section, `projects.create_project_anonymous`) or [MCP_QUICKSTART.md](MCP_QUICKSTART.md).

2. **Read and write project data**
   - REST:  
     `GET https://agentstack.tech/api/projects/{project_id}/data` — all `project.data` or by `?path=key`.  
     `PATCH https://agentstack.tech/api/projects/{project_id}/data` with body `{ "path": "key", "value": <value> }`.
   - Or key-value:  
     `GET https://agentstack.tech/api/dna/data?key=project.data.key&project_id={project_id}`  
     `POST https://agentstack.tech/api/dna/data` with body `{ "key": "project.data.key", "value": <value>, "project_id": <id> }`.
   - MCP: `projects.get_project`, `projects.update_project` (field `data`).

3. **User data in project**
   - REST:  
     `GET /api/projects/{project_id}/users/me/data` (optional `?path=...`),  
     `PATCH /api/projects/{project_id}/users/me/data` with `{ "path": "...", "value": ... }`.
   - Key-value: key `user.data.<path>`, `GET/POST /api/dna/data` (see [DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md)).

---

## Example: mobile game data storage

Store player progress (level, coins, inventory) in the current user's project data — in `user.data.game.progress`.

### Option 1: REST (path/value)

**Write progress:**
```http
PATCH https://agentstack.tech/api/projects/{project_id}/users/me/data
Authorization: Bearer <token>
Content-Type: application/json

{
  "path": "game.progress",
  "value": {
    "level": 5,
    "coins": 1000,
    "inventory": ["sword", "potion"]
  }
}
```

**Read progress:**
```http
GET https://agentstack.tech/api/projects/{project_id}/users/me/data?path=game.progress
Authorization: Bearer <token>
```

### Option 2: DNA Key-Value API

**Write:**
```http
POST https://agentstack.tech/api/dna/data
Authorization: Bearer <token>
Content-Type: application/json

{
  "key": "user.data.game.progress",
  "value": { "level": 5, "coins": 1000, "inventory": ["sword", "potion"] },
  "project_id": 1025
}
```

**Read:**
```http
GET https://agentstack.tech/api/dna/data?key=user.data.game.progress&project_id=1025
Authorization: Bearer <token>
```

### Option 3: MCP (for agents and plugins)

Use tools for working with project and user data per [MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md) (e.g., get/update project with the `data` field or calls that write to user.data).

---

## Rules / Logic

Rules and logic (when/then, triggers) are available via the Logic Engine API: prefix **`/api/logic`**. For listing and creating rules, execution — see the Logic Engine documentation and [CONTEXT_FOR_AI.md](plugins/CONTEXT_FOR_AI.md) (Rules Engine domain → `logic.*`).

---

**Version:** 0.2 — guide to actual endpoints, no versioning in paths.  
**Source of truth for paths:** `agentstack-core` code (projects_endpoints, dna_api_endpoints, core_app).
