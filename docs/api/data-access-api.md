# Data Access API — Field-Level Access Policy (FAP)

**Swagger:** `/docs` → tag **DataAccess** (`/api/data-access/*`)

## Purpose

Declarative per-field access: policies live in `project.data.config.field_access_policy`. Global defaults for *new* resource keys live on the ecosystem project in `config.field_access_defaults`.

| Concern | Doc |
|--------|-----|
| Policy format, ecosystem masking, MCP | [FIELD_ACCESS_POLICY.md](../FIELD_ACCESS_POLICY.md) |
| Overview (L1/L2/L3) | [ACCESS_AND_FIELD_POLICY.md](../ACCESS_AND_FIELD_POLICY.md) |
| Try requests in the browser | [Swagger UI](https://agentstack.tech/swagger) — tag **DataAccess** |
| Cache invalidation | Invalidate client caches after policy writes (platform convention) |

## Authentication and authorization layers

All routes require a valid session or Bearer token. **403** if the user lacks the required project role.

Authorization runs in three ordered layers before any field value is returned:

1. **L1 — API key service caps** (`key_service_caps`): if the project API key has a `service_caps` list, only those services are reachable. Enforced via `require_key_service_cap("data_access")` (add to routes that should be cap-gated). A `None` list means unrestricted.
2. **L2 — RBAC**: role-based per-project gate (see Access column below).
3. **L3 — FAP**: field-level response masking applied by `FieldAccessService.apply_mask()`.

**Project API key scope:** a project API key is scoped to its own project. Any request using key for project A with `?project_id=B` (B ≠ A) is rejected with 403 (`_assert_key_project_scope`). User API keys and browser sessions are not restricted to a single project.

## Endpoints (summary)

| Method | Path | Access |
|--------|------|--------|
| GET | `/api/data-access/policy?project_id=` | Read on project |
| PUT | `/api/data-access/policy/{resource}?project_id=` | Admin or **owner** |
| DELETE | `/api/data-access/policy/{resource}?project_id=` | Admin or owner |
| GET | `/api/data-access/defaults-template` | Read on **project 1** |
| PUT | `/api/data-access/defaults-template` | Admin or owner on **project 1** |
| POST | `/api/data-access/defaults-template/apply?target_project_id=` | Admin or owner on **target** (not 1) |
| POST | `/api/data-access/check?project_id=` | Read on project |
| POST | `/api/data-access/preview?project_id=` | Read on project |
| POST | `/api/data-access/emit?project_id=` | **Read on project** — Rules Engine + FAP v1.2 `field_triggers` |
| GET | `/api/data-access/triggers?project_id=` | Read on project |
| PUT | `/api/data-access/triggers/{resource}?project_id=` | Admin or owner |
| DELETE | `/api/data-access/triggers/{resource}?project_id=` | Admin or owner |
| POST | `/api/data-access/trigger-test?project_id=` | Read on project (dry-run) |
| POST | `/api/data-access/cache/invalidate?project_id=` | Admin or owner |
| GET | `/api/data-access/cache/stats` | **Admin or owner on ecosystem project (id 1)** |

## Global template flow

1. Ecosystem operator **PUT**s `defaults-template` (JSON body with `default_access`, `globals`, `resources`, optional `field_triggers`).
2. Project owner opens Dashboard → Field access → **Apply global FAP template**, or calls **POST …/apply** — only **missing** `resources.<name>` and `field_triggers.<name>` keys are copied.

## OpenAPI

Request body for **PUT `/defaults-template`** includes a **Schema example** (Swagger “Example Value”) for `project_payload`, `orders`, dotted globals.

## Pagination convention

List-style Data Access / DNA responses should prefer the shared **`PaginatedResponse`** envelope (cursor-first) documented in OpenAPI `components.schemas.PaginatedResponse`. Legacy offset/limit lists remain valid but should be marked explicitly. See [OPENAPI.md](../OPENAPI.md) (pagination + ProblemDetails).

## Related MCP tools

`data_access.get_defaults_template`, `data_access.set_defaults_template`, `data_access.apply_defaults_template`, `data_access.get_triggers`, `data_access.set_triggers` — see [MCP_TOOLS.md](../MCP_TOOLS.md).
