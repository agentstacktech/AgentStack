# Field-Level Access Policy

**Version:** 1.0  
**Philosophy:** Creation over Conflict (v0.2.56) · Elegant Minimalism · 8DNA · Decomposition

---

## Overview

Field-Level Access Policy (FAP) adds a second layer of access control on top of the existing route-level RBAC.

### Three-layer authorization model

Requests flow through three independent gates before a field value is returned:

```
Request → [L1: API key caps — service/function gate]
        → [L2: RBAC — route/action gate]
        → [L3: FAP — field-level mask]
        → Response
```

**L1 (API key `service_caps`):** Project API keys may restrict which services are accessible at all, overriding even admin roles. Enforced in `session_auth.py` via the `require_key_service_cap(service)` dependency **before** RBAC. Configured by project owner as a list stored in `config.api_keys[key_id].service_caps` (UI: checkboxes). `None` = all services allowed (default).

**L2 (RBAC):** Standard role-based permissions per project.

**L3 (FAP):** Field-level mask applied by `FieldAccessService.apply_mask()` after the route logic.

**Project API key scope:** A project API key is scoped to exactly one project. Using a key from project A to access FAP data of project B is rejected with 403 (`_assert_key_project_scope`). User API keys and full sessions are not restricted to a single project.

### Who can do what (API)

| Action | Requirement |
|--------|----------------|
| `GET /api/data-access/policy?project_id=` | **Read** access to that project |
| `POST` preview, check | **Read** on project |
| `PUT/DELETE` policy resource | **Admin** RBAC on project **or** **project owner** |
| `POST /emit` | **Read** on project (authz added v0.4) |
| `GET/PUT/DELETE /api/data-access/triggers*`, `POST /trigger-test` | **Read** (GET/test) / **admin or owner** (PUT/DELETE) on project |
| `GET/PUT /api/data-access/defaults-template` | **Read** / **admin or owner** on **ecosystem project (id 1)** |
| `POST .../defaults-template/apply?target_project_id=` | **Admin or owner** on **target** project (not id 1) |
| `POST /cache/invalidate` | **Admin or owner** on project |
| `GET /cache/stats` | **Admin or owner** on **ecosystem project (id 1)** (global stats — ecosystem-admin only) |

**Dashboard:** project owners and project admins edit FAP for their selected project. **Admin panel:** ecosystem FAP (project 1) and global template editor.

### Global template (`field_access_defaults`)

Stored on the ecosystem project at `project.data.config.field_access_defaults` (shape: `default_access`, `globals`, `resources`, optional `field_triggers` for v1.2). It does **not** apply automatically to new projects. **Apply** (`POST .../defaults-template/apply`) copies only **missing** `resources.<name>` and `field_triggers.<name>` keys into a user project. Configure the template in **Admin → FAP — global template**; owners use **Dashboard → Field access → Apply global FAP template** on their project.

**MCP:** `data_access.get_defaults_template`, `data_access.set_defaults_template`, `data_access.apply_defaults_template`, `data_access.get_triggers`, `data_access.set_triggers` (same semantics; service context).

### Layers

| Layer | What it protects | How it works |
|---|---|---|
| **L1: API key caps** | Entire services/functions | `service_caps` list on key; `require_key_service_cap("scheduler")` dep; enforced before RBAC |
| **L2: Route RBAC** | Entire endpoints | `require_read_permission`, `require_admin_permission` FastAPI deps — O(0) DB calls |
| **L3: Field Policy (FAP)** | Individual response fields | Declarative JSON mirror of data structure stored in 8DNA; applied via `FieldAccessService.apply_mask()` |

### Three levels of complexity

| Level | Use case | Tools needed |
|---|---|---|
| 1 | Online store, small site | `field_access_policy` JSON + `apply_mask()` in handlers |
| 2 | Business logic side-effects | Rules Engine `data_event` triggers + `data_access_processor` |
| 3 | Dynamic conditions | `condition` expression in policy + `FieldGuard` in custom processors |

---

## Storage (8DNA)

```
project.data.config.field_access_policy
```

Same pattern as `project.data.config.rbac` — no extra tables, no migrations.

---

## Cache Integration

| Property | Value |
|---|---|
| Cache layer | L1 "neural" (shared with RBAC, MCP discovery, sessions) |
| Namespace | `field_access:policy` |
| Key | `field_access_policy:{project_id}` |
| Tags | `["field_access", "project:{project_id}"]` |
| TTL | 3600 s (invalidated-on-write anyway) |
| Pattern | Loader pattern — cache handles miss → load → store atomically |
| Invalidation | `invalidate_by_tag("project:{project_id}")` on every write |

**Key insight (Elegant Minimalism):** Because RBAC cache and field_access cache share
the same L1 "neural" layer, RBAC role-change invalidations (`project:{project_id}`)
**automatically cascade** to field_access policy entries — no cross-service coupling.

**Hot-path optimisation:** `PolicyEvaluator._check_role_string()` uses `@lru_cache`
on pure (role_str, role, is_authenticated, is_owner) inputs — role hierarchy comparisons
are memoised across the process lifetime (2 048 entry LRU). Bitmap checks bypass the
LRU and go through the full evaluation path.

---

## Dashboard UI vs DNA schemas

The **Field access** widget in Dashboard V3 lists only field rules that exist in the saved policy: `resources.<name>.<field>`. It does **not** auto-load column names from DNA entities or OpenAPI — you add rows manually, use quick templates, or paste JSON.

An empty resource entry (`resources.orders: {}`) is valid; the UI shows a single `__default__` row and a short notice until you add field rules.

**Backlog (optional):** “Import field keys from DNA” would be a separate feature (endpoint or wizard), not implied by the current editor.

If API calls return **401** after a long idle period, refresh the session (reload the app or sign in again).

---

## Version 1.1 — `globals` and `path_rules`

**Storage:** same `field_access_policy` object. Set `version` to `"1.1"` when using `globals` or per-resource `path_rules` (the server may set this automatically).

### `globals` (project-wide)

| Field | Values | Meaning |
|-------|--------|---------|
| `nested_path_mode` | `flat` (default) | Legacy: each JSON key uses one policy entry by **segment name** only; nested objects reuse the same flat map (name collisions possible). |
| | `dotted` | Paths are **dot-separated** from the root of the masked object (e.g. `config.api.secret`). |
| `max_mask_depth` | integer 8–512 (default 128) | Recursion cap for `apply_mask` on deep trees. |
| `default_access` | optional | Same role as root `default_access`; root key remains the primary fallback if unset here. |

### Per-resource `path_rules`

Inside `resources.<name>`, optional ordered array:

```json
"path_rules": [
  { "pattern": "config.api.secret", "access": "admin" },
  { "pattern": "config.**", "access": "user" }
]
```

Pattern segments may use letters, digits, underscore, hyphen (not in `*` / `**` tokens).

**Pattern syntax (dot-separated segments):**

| Pattern | Matches |
|---------|---------|
| `a.b.c` | Exact path `a.b.c` only |
| `config.*` | Exactly two segments: `config` + one (e.g. `config.api`, not `config.api.key`) |
| `config.**` | `config`, `config.api`, `config.api.key`, … |

**Precedence in `dotted` mode for path `P`:**

1. Exact string key `P` on the resource policy whose value is a descriptor (string or `{read,write,…}`).
2. **First** `path_rules[]` entry whose `pattern` matches `P` (order matters — list specific rules before broad `**`).
3. `__default__` on the resource.
4. Project `default_access` (root or `globals.default_access`).

**Flat mode:** `path_rules` are ignored; behaviour unchanged from v1.0.

**Lists:** Elements that are objects are masked with a path prefix equal to the list’s field name (no numeric index in the path). Scalars in lists share the list field’s path for the allow check.

### Ecosystem zone (`data.ecosystem` on project_id = 1)

The **ecosystem project** stores shared/protected data under `project.data.ecosystem` (buffs, templates, etc.). **Reads** through `GET /api/projects` and `GET /api/projects/{id}` apply FAP only when you define **`resources.ecosystem`** on the **ecosystem project’s** `field_access_policy`.

| Situation | Behaviour |
|-----------|-----------|
| User project (`project_id ≠ 1`) | `ecosystem` is **omitted** from serialized project payloads (leak protection). |
| Ecosystem project (`project_id = 1`) — Projects API | If `resources.ecosystem` exists → `apply_mask` on the `ecosystem` subtree per caller role; **if absent → the full `ecosystem` subtree is returned unchanged** (legacy compatibility — not deny-by-default). |
| Ecosystem project — `GET /dna/data` for `project.data.ecosystem*` | **Ecosystem owner** (project 1): raw value. **Others:** must have **read** on project 1; value is masked with the same FAP as Projects API (if no `resources.ecosystem`, full subtree). **`is_ecosystem` alone does not bypass** this path. |
| Ecosystem project — `POST /dna/data` for `project.data.ecosystem*` | **Forbidden (403)** for normal session users. Server-side paths (BuffProtein, admin flows) must use dedicated APIs or internal writes — not DNA key-value for this zone. |

**Strict model:** Until you add `resources.ecosystem`, any user with **read** on project 1 sees the **entire** ecosystem subtree via Projects API and (with read) via DNA GET. To enforce least privilege, define **`resources.ecosystem`** with **`"__default__": "deny"`** (or equivalent) and **explicit** rules only for fields that should be visible per role (e.g. `public`, `path_rules`).

Policy is always loaded from **project 1**. Use dotted mode + `path_rules` for nested keys (e.g. `buffs`, `templates.*`).

### Example (dotted + wildcards)

```json
{
  "version": "1.1",
  "default_access": "deny",
  "globals": { "nested_path_mode": "dotted" },
  "resources": {
    "project_payload": {
      "path_rules": [
        { "pattern": "config.y", "access": "deny" },
        { "pattern": "config", "access": "user" },
        { "pattern": "config.**", "access": "public" }
      ],
      "__default__": "deny"
    }
  }
}
```

Payload `{ "config": { "x": 1, "y": 2 } }` → user sees `config.x` and `config` wrapper; `config.y` hidden.

---

## Field Triggers (v1.2)

Declarative **reactions** tied to the same resource/field mental model as FAP. Stored in `field_access_policy.field_triggers` (no extra tables). The server sets `version` to **`1.2`** when `field_triggers` is a non-empty object.

### How it differs from Rules Engine `data_event`

| | **Field triggers (FAP v1.2)** | **Rules Engine `data_event`** |
|---|---|---|
| Storage | `field_access_policy.field_triggers` | `project.data.logic` |
| Shape | Resource → field pattern → list of small trigger defs | Full rules with `triggers` + `space` graphs |
| Execution | `POST /api/data-access/emit` runs Logic Engine **then** matching field triggers (sync by default) | Compiled Python + processor pipeline |
| Use case | Set field, copy field, emit signal, invalidate cache, call one processor/rule | Complex multi-step automation |

### REST

| Method | Path | Auth |
|--------|------|------|
| GET | `/api/data-access/triggers?project_id=` | Project **read** |
| PUT | `/api/data-access/triggers/{resource}?project_id=` | Project **admin** or **owner** |
| DELETE | `/api/data-access/triggers/{resource}?project_id=` | Admin or owner |
| POST | `/api/data-access/trigger-test?project_id=` | Project **read** (dry-run: match + `condition`, **no** actions) |
| POST | `/api/data-access/emit?project_id=` | Authenticated; extends response with `field_triggers` |

### JSON shape

```json
{
  "version": "1.2",
  "resources": { "orders": { "__default__": "user" } },
  "field_triggers": {
    "orders": {
      "status": [
        {
          "id": "optional-uuid",
          "name": "Mark completed",
          "on": ["updated"],
          "condition": "{{data.status}} == \"done\"",
          "priority": 10,
          "async": true,
          "enabled": true,
          "actions": [
            { "type": "set_field", "path": "meta.closed_at", "value": "{{now()}}", "scope": "data" },
            { "type": "emit_event", "signal": "orders.closed", "payload": "{{data}}" }
          ]
        }
      ],
      "*": [
        { "on": ["read"], "actions": [{ "type": "invalidate_cache", "invalidate_scope": "policy" }] }
      ]
    }
  }
}
```

- **Resource key** (`orders`): same as FAP `resources.*`.
- **Pattern key** (`status`, `config.**`, `*`): same glob rules as `path_rules` (`*` = one segment, `**` = suffix). Pattern `*` matches **every** event for that resource.
- **`on`:** `read`, `write` (matches `created` / `updated` / `deleted` from emit), or any of `created`, `updated`, `deleted`, `read`.
- **`condition`:** optional. After expanding templates, evaluated with the same safety constraints as FAP descriptor `condition` (no builtins).
- **`async`:** if `true` (default), actions run in `asyncio.create_task` when `evaluate_triggers(..., run_async=True)`. **`/emit` uses `run_async=false`** so actions complete before the HTTP response.

### Template placeholders in `condition`, `value`, `payload`

- `{{data.path.to.field}}` — from the event payload.
- `{{ctx.user_id}}`, `{{ctx.role}}`, `{{ctx.project_id}}`, `{{ctx.is_owner}}` — from `AccessContext`.
- `{{now()}}` — UTC ISO timestamp.

### Action types

| `type` | Fields | Behaviour |
|--------|--------|-----------|
| `set_field` | `path`, `value`, optional `scope` (`data` \| `entity`), `entity_uuid`, `table_name` | Writes into event `data` (deep merge path) or updates a DNA row by UUID. |
| `copy_field` | `from_path`, `to_path` | Within event `data`. |
| `emit_event` | `signal`, optional `payload` | `NeuralEventSystem` `DATA_CHANGE` with metadata. |
| `call_processor` | `processor`, `params` | `ProcessorsService` + `processor.execute`. |
| `call_logic_rule` | `rule_id` | First trigger with `next` on that logic rule (`LogicEngineService.execute_logic_rule_by_id`). |
| `invalidate_cache` | `invalidate_scope`: `policy` \| `project` | FAP tag invalidation for the project. |

### Global template

`field_access_defaults` on the ecosystem project may include **`field_triggers`**. `POST /api/data-access/defaults-template/apply` copies **missing** resource keys from both `resources` and `field_triggers` (same merge semantics as resources).

### MCP

- `data_access.get_triggers` — `project_id`
- `data_access.set_triggers` — `project_id`, `resource`, `triggers` (pattern → list)

---

## Policy Format

### Shorthand (most common)

```json
{
  "version": "1.0",
  "default_access": "deny",
  "resources": {
    "products": {
      "id":             "public",
      "name":           "public",
      "price":          "user",
      "cost_price":     "admin",
      "supplier_notes": "admin",
      "__default__":    "deny"
    },
    "orders": {
      "id":              "owner|admin",
      "total":           "owner|admin",
      "items":           "owner|admin",
      "profit_margin":   "admin",
      "internal_notes":  "admin"
    }
  }
}
```

### Extended (read ≠ write, conditions)

```json
{
  "salary": {
    "read":      "admin",
    "write":     "admin",
    "condition": "{{user.role}} == 'admin' or {{user.is_owner}}"
  }
}
```

### Special descriptor values

| Value | Meaning |
|---|---|
| `"public"` | Anyone, including anonymous |
| `"authenticated"` | Any logged-in user |
| `"owner"` | The resource's owner (`resource_owner_id == user_id`) |
| `"none"` / `"deny"` | No access (blocked) |
| `"roleA\|roleB"` | OR condition — either role satisfies |
| `"__default__"` | Catch-all for fields not listed in the resource policy |

### Role hierarchy (ascending)

`public` → `authenticated` → `viewer` → `member` → `user` → `staff` → `admin` → `owner`

A user with role `admin` satisfies any descriptor at or below `admin` in the hierarchy.

---

## REST API

All endpoints require authentication. **`project_id`** is always a query parameter unless noted.

**Swagger:** `/docs` — group **DataAccess** (summaries, auth notes, **example body** for `PUT /defaults-template`).

**Human-readable index:** [docs/api/data-access-api.md](api/data-access-api.md).

```
GET  /api/data-access/policy?project_id=42
     → Full policy. **Auth:** read on project.

GET  /api/data-access/policy?project_id=42&resource=orders
     → Single `resources.orders`.

PUT  /api/data-access/policy/{resource}?project_id=42
     Body: { "resource_policy": { ... }, "default_access": "deny", "globals": { ... }? }
     → **Auth:** project admin or owner. Upserts; invalidates cache.

DELETE /api/data-access/policy/{resource}?project_id=42
     → **Auth:** admin or owner.

GET  /api/data-access/defaults-template
     → Global template from project 1 DNA. **Auth:** read on project 1.

PUT  /api/data-access/defaults-template
     Body: { "default_access", "globals", "resources" } — see Swagger example.
     → **Auth:** admin or owner on project 1.

POST /api/data-access/defaults-template/apply?target_project_id=42
     Body: {} — merges missing template resources into target. **Auth:** admin or owner on target (not id 1).

POST /api/data-access/check?project_id=42
     Body: { "field_path": "orders.profit_margin", "user_role": "user", "permission": "read" }
     → Returns { "allowed": false, ... }

POST /api/data-access/preview?project_id=42
     Body: { "resource": "orders", "user_role": "user", "sample_data": { ... } }
     → Per-field access breakdown with optional actual values.
     Optional: `draft_resource_policy` + `draft_default_access` — evaluate as if that
     resource policy were active (Dashboard editor preview before Save).
     **Dotted mode:** preview always includes one row per `path_rules[]` pattern
     (`kind: path_rule`) and per literal dotted key on the resource (e.g. `meta.owner_id`);
     `sample_data` adds rows for every dotted path discovered in the JSON tree (merged,
     no duplicate path).

POST /api/data-access/emit?project_id=42
     Body: { "resource": "orders", "action": "created", "event_data": { ... } }
     → Fires data_event triggers in Rules Engine
```

### Dashboard (Field access tab)

- **Save** calls `PUT`; the response body includes the merged full policy — the UI updates immediately.
- **Preview** sends `draft_resource_policy` so results match the table/JSON **before** Save.
- **Remove resource policy** calls `DELETE` for the selected resource.

---

## MCP Tools

```
data_access.set_policy              — create / update resource policy
data_access.get_policy              — retrieve policy (optional resource filter)
data_access.check_field             — boolean access check for role + field
data_access.test_mask               — preview per-field access map for a role
data_access.get_defaults_template   — read global FAP template (ecosystem DNA)
data_access.set_defaults_template   — write global template
data_access.apply_defaults_template — merge missing resources into a project
```

Details: [MCP_TOOLS.md](MCP_TOOLS.md). All tools use `agentstack.execute`.

---

## Python Usage

### AccessContext

```python
from shared.field_access_service import AccessContext, field_access_service

ctx = AccessContext.from_current_user(current_user, resource_owner_id=order.owner_id)
policy = await field_access_service.get_policy(project_id)
masked_order = field_access_service.apply_mask(order_dict, "orders", ctx, policy)
```

### FieldGuard (inline wrapping)

```python
from shared.field_access_service import FieldGuard

guard = FieldGuard(value=order.profit_margin, read_role="admin", write_role="admin")
visible = guard.resolve(ctx)   # None for non-admins
```

### Applying to a list

```python
masked_orders = field_access_service.filter_collection(
    orders_list, "orders", ctx, policy, owner_id_field="user_id"
)
```

### Check a single field

```python
can_read = field_access_service.check_field("orders.profit_margin", "read", ctx, policy)
```

---

## Rules Engine Integration

### New trigger type: `data_event`

```json
{
  "type": "data_event",
  "resource": "orders",
  "action": "created",
  "filter": { "status": "pending" },
  "_label": "Order Created",
  "next": { "..." : "..." }
}
```

| Field | Values | Description |
|---|---|---|
| `resource` | any string | Resource name (e.g., `"orders"`) |
| `action` | `created` \| `updated` \| `deleted` \| `read` \| `*` | CRUD action to react to |
| `filter` | object (optional) | Field equality conditions on `event_data` |

**Emitting data events** from your handler:

```python
from services.logic_engine_service import LogicEngineService

engine = LogicEngineService()
await engine.execute_data_event(
    resource="orders",
    action="created",
    event_data=new_order,
    project_id=project_id,
    user_id=user_id,
)
```

Or via the REST endpoint `POST /api/data-access/emit`.

### New processor: `data_access_processor`

```json
{
  "type": "processor",
  "processor": "data_access_processor",
  "action": "apply_mask",
  "parameters": {
    "resource": "orders",
    "data": "{{command_data.result}}",
    "user_role": "{{user.role}}"
  },
  "next": { "..." : "..." }
}
```

#### Actions

| Action | Required params | Returns |
|---|---|---|
| `apply_mask` | `resource`, `data` | `{ data: masked_dict }` |
| `check_field` | `field_path`, `permission` | `{ allowed: bool }` |
| `filter_collection` | `resource`, `data` (list) | `{ data: masked_list, count }` |

---

## Data Flow

```
Client Request
     │
     ▼
Auth + Route RBAC (require_*_permission)
     │
     ▼
Endpoint Handler  ──reads──►  8DNA JSONB
     │                             │
     │                       raw_data
     │                             │
     ▼                             ▼
FieldAccessService.apply_mask(raw_data, resource, ctx, policy)
     │                       ▲
     │                       │
     │               field_access_policy
     │                (NeuralCache / DB)
     ▼
Masked Response  ──────────►  Client

Rules Engine (data_event trigger)
     │
     ▼
data_access_processor.apply_mask / check_field / filter_collection
     │
     ▼
Webhook / Notify / other processors
```

---

## Example: Online Store

```json
{
  "version": "1.0",
  "default_access": "deny",
  "resources": {
    "products": {
      "id":             "public",
      "name":           "public",
      "description":    "public",
      "image_url":      "public",
      "price":          "authenticated",
      "stock":          "authenticated",
      "cost_price":     "admin",
      "supplier_id":    "admin",
      "internal_notes": "admin",
      "__default__":    "deny"
    },
    "orders": {
      "id":             "owner|admin",
      "status":         "owner|admin",
      "items":          "owner|admin",
      "total":          "owner|admin",
      "user_id":        "owner|admin",
      "shipping":       "owner|admin",
      "profit_margin":  "admin",
      "cost":           "admin",
      "__default__":    "admin"
    }
  }
}
```

---

## Related Docs

- [api/data-access.md](api/data-access.md) — field-level policy REST API
- [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md) — MCP actions for data access
- [MCP_TOOLS.md](MCP_TOOLS.md) — MCP tools reference (data_access.*)
