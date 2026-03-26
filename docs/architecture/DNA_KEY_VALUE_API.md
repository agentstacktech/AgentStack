# DNA Key-Value API (sdk.db.get / sdk.db.set)

**Version:** 0.1  
**Related:** 8DNA · [MCP and ecosystem index](../MCP_AND_ECOSYSTEM.md) · [OpenAPI](../OPENAPI.md) (`/api/dna/data` in [Swagger](https://agentstack.tech/swagger))

---

## Overview

The DNA API exposes a key-value interface for built apps: keys of the form `project.data.<path>` or `user.data.<path>` map to JSON in the platform’s **project** and **per-user (per-project)** stores. This corresponds to SDK usage: `sdk.db.get(key)` and `sdk.db.set(key, value)`.

## Endpoints

DNA API base path: **`/api/dna`** (no version in paths).

| Method | Full path   | Description |
|--------|-------------|-------------|
| GET    | `GET /api/dna/data` | Get value at key. Query: `key` (required), `project_id` (required for `project.data.*`). |
| POST   | `POST /api/dna/data` | Set value at key. Body: `{ "key": "...", "value": <any>, "project_id": <optional> }`. |

## Key format

- **project.data.&lt;path&gt;** — Dot-separated path into the project entity’s `data` (e.g. `project.data.config.theme`). Requires `project_id` (query or body).
- **user.data.&lt;path&gt;** — Dot-separated path into the current user’s project-scoped `data` (e.g. `user.data.preferences.lang`). Uses current user and project from session; `project_id` optional to override.

## Permissions

- **project.data.*** — Caller may only read/write data for their own project: `current_user.project_id == project_id` or `current_user.is_ecosystem`. Otherwise 403.
- **user.data.*** — Caller may only read/write the authenticated user’s own data (user_id and project_id from session). No access to other users’ data.

## Source of truth

- **Project keys** — one JSON document per project; `project.data.*` paths read/write inside it.
- **User keys** — one JSON document per **user + project** pair; `user.data.*` paths read/write inside it. A missing document is created on first set for `user.data`.

## Design notes

- **8DNA:** single key-value surface for app data; permissions are enforced at the API boundary.
