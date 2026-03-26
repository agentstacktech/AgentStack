# Access Control — Three Layers & Field Access Policy (FAP)

This document describes how **authentication** becomes **authorization** in AgentStack: project API keys, roles (RBAC), and **Field Access Policy** (FAP) for per-field visibility in API responses.

**Related:** [OPENAPI.md](OPENAPI.md) (Swagger / ReDoc / schema) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) (sandbox routing and protected fields) · [MCP_TOOLS.md](MCP_TOOLS.md) (`data_access.*` tools).

---

## Contents

1. [Why three layers](#why-three-layers)
2. [L1 — API key service caps](#l1--api-key-service-caps)
3. [L2 — RBAC (roles)](#l2--rbac-roles)
4. [L3 — Field Access Policy (FAP)](#l3--field-access-policy-fap)
5. [Order of enforcement](#order-of-enforcement)
6. [Project API key scope](#project-api-key-scope)
7. [Where policies live (8DNA)](#where-policies-live-8dna)
8. [Global FAP template](#global-fap-template)
9. [REST API (summary)](#rest-api-summary)
10. [MCP tools](#mcp-tools)
11. [Examples](#examples)

---

## Why three layers

Different concerns are separated so you can:

- **Limit automation keys** to specific services (payments only, no admin APIs, etc.).
- **Reuse one role model** (`owner`, `admin`, `user`, custom roles) across endpoints.
- **Hide or mask fields** in JSON responses by role without duplicating handlers for every field.

---

## L1 — API key service caps

**What:** For **project API keys**, optional list `config.api_keys[key_id].service_caps` names allowed **services** (e.g. `payments`, `scheduler`, `analytics`, `data_access`, …).

**When:** Checked **first** during authentication, **before** RBAC and FAP.

**Semantics:**

- If `service_caps` is **empty or `None`**, the key is treated as **unrestricted** (backward compatible).
- If the list is **non-empty**, the request must target a service in that list or the call fails (typically **403**).

**Typical use:** CI keys that may call payments but not user management; integration keys scoped to analytics only.

---

## L2 — RBAC (roles)

**What:** Per-project **roles** and permissions: who may **read** a project, **write** data, **admin**, invite users, etc.

**When:** After L1 passes, **endpoint-level** permission checks enforce who may call each route.

**Typical roles:** `owner`, `admin`, `user` (exact set depends on product configuration).

FAP does **not** replace RBAC: you still need route permission to hit the endpoint; FAP then shapes the **body** of allowed responses.

---

## L3 — Field Access Policy (FAP)

**What:** Declarative rules in `project.data.config.field_access_policy` that define, per **resource** (e.g. `project_payload`, `orders`), which **fields** each role may **read** (and how they appear — raw, masked, hidden).

**When:** After successful route logic, the server **applies the field policy** to the outgoing JSON so clients only see fields allowed for the current role.

**Extras (policy v1.2+):** Optional `field_triggers` can drive side effects together with the Rules Engine (`POST /api/data-access/emit` and related flows).

---

## Order of enforcement

```
Incoming request
    → L1: API key service_caps (if project API key)
    → L2: RBAC — allowed to call this route / project?
    → Handler runs
    → L3: FAP — field masking on response payload
    → Response
```

---

## Project API key scope

A **project API key** is bound to **one** project. Requests that use a **different** `project_id` than that key’s project receive **403**.

**User API keys** and **browser sessions** are not limited to a single project in the same way.

---

## Where policies live (8DNA)

| Data | Location |
|------|----------|
| Per-project FAP | `project.data.config.field_access_policy` |
| Global template (ecosystem) | `project.data.config.field_access_defaults` on the **ecosystem project** (id `1`) |

Policies are stored in **8DNA** project JSON (alongside RBAC), not as a separate product surface you query by table name.

---

## Global FAP template

The ecosystem project holds a **template** (`field_access_defaults`: `default_access`, `globals`, `resources`, optional `field_triggers`).

- It does **not** auto-apply to new projects.
- **Apply** (`POST /api/data-access/defaults-template/apply?target_project_id=`) copies only **missing** `resources.<name>` and `field_triggers.<name>` keys into the target project.

**UI (typical):** Admin configures the global template; project owners use **Dashboard → Field access → Apply global FAP template**.

---

## REST API (summary)

**Interactive docs:** [Swagger UI](https://agentstack.tech/docs) → filter by tag **DataAccess** — base path `/api/data-access/*`. See also [OPENAPI.md](OPENAPI.md) (ReDoc, `openapi.json`).

All routes require a valid session or Bearer token. **403** if the user lacks the required project role.

| Method | Path | Typical access |
|--------|------|----------------|
| GET | `/api/data-access/policy?project_id=` | Read on project |
| PUT | `/api/data-access/policy/{resource}?project_id=` | Admin or owner |
| DELETE | `/api/data-access/policy/{resource}?project_id=` | Admin or owner |
| POST | `/api/data-access/check`, `/preview`, `/emit`, `/trigger-test` | Read on project (emit: Rules + FAP v1.2 triggers) |
| GET/PUT | `/api/data-access/defaults-template` | Read / admin or owner on **ecosystem project (1)** |
| POST | `/api/data-access/defaults-template/apply?target_project_id=` | Admin or owner on **target** (not project 1) |
| POST | `/api/data-access/cache/invalidate?project_id=` | Admin or owner |
| GET | `/api/data-access/cache/stats` | Admin or owner on ecosystem project |

For full parameter lists and bodies, use **[Swagger](https://agentstack.tech/docs)** or **[openapi.json](https://agentstack.tech/openapi.json)** on your deployment ([OPENAPI.md](OPENAPI.md)).

---

## MCP tools

Declarative policy management from AI clients:

| Tool (domain) | Purpose |
|-----------------|---------|
| `data_access.get_policy` / `set_policy` | Read/write full or per-resource policy |
| `data_access.check_field` / `test_mask` | Validate visibility for a role |
| `data_access.get_defaults_template` / `set_defaults_template` / `apply_defaults_template` | Global template on ecosystem project |
| `data_access.get_triggers` / `set_triggers` | FAP v1.2 field triggers |

See [MCP_TOOLS.md](MCP_TOOLS.md) for parameters and examples.

---

## Examples

### Conceptual policy shape (simplified)

Policies are JSON mirrors of your data shape under **`resources.<name>`**, plus optional **`globals`** and **`default_access`**:

```json
{
  "version": "1.2",
  "default_access": "deny",
  "globals": {
    "project.name": { "owner": "read", "admin": "read", "user": "read" }
  },
  "resources": {
    "project_payload": {
      "config.payment_provider": { "owner": "read", "admin": "read", "user": "mask" },
      "config.public_theme": { "owner": "read", "admin": "read", "user": "read" }
    }
  }
}
```

Exact keys and role names must match your project’s RBAC and resource names used in serializers.

### L1 + L2 + L3 mental model

1. Script uses a project API key with `service_caps: ["payments"]` only → calls to `data_access` **fail** until `data_access` is added to caps.
2. User has **read** on the project → may `GET` policy and call **`POST /preview`**.
3. Same user is role **`user`** → FAP may **mask** `config.api_secret` in responses while **`owner`** sees it.

### Sandbox and secrets

In **sandbox** contexts, decrypted secrets are not exposed via query flags; use **`POST /api/sandbox/protected-check`** for hash verification. See [SANDBOX_AND_ENVIRONMENTS.md#protected-field-security](SANDBOX_AND_ENVIRONMENTS.md#protected-field-security).

---

## Subscription note

Feature gating (which tier may use custom RBAC, user management, etc.) follows **your plan** and in-app **Pricing**. Who may **edit** FAP follows **project/ecosystem admin** rules in the product.
