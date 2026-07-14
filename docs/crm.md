# CRM — project-scoped contacts, deals, and pipelines

**Platform line:** 0.4.14+ · **Genetic tag:** `core.crm.hub.gen1`

CRM lives on the unified 8DNA project slice. Contacts, companies, deals, pipelines, and activities are child rows — not a separate database product.

## REST

Project-scoped routes:

- `GET/POST /api/projects/{projectId}/crm/contacts`
- `GET/PATCH/DELETE /api/projects/{projectId}/crm/contacts/{contactId}`
- Board, pipelines, deals, activities — same prefix pattern

Requires project membership and CRM capability. When platform `CRM_MODE=off`, all CRM REST returns **503**.

## MCP

Discover via `GET /mcp/actions` — domain **`crm.*`** (contacts, deals, board, export, etc.).

Example intent: "list CRM contacts for project 42" → `crm.contact.list` with `project_id`.

## SDK

Use `@agentstack/sdk` CRM module (`sdk.crm`) for typed client helpers aligned with REST/MCP parity.

## Boundaries (important)

| Concept | Where it lives |
|---------|----------------|
| CRM contact / deal | `/api/projects/{id}/crm/*`, MCP `crm.*` |
| Marketplace **deal** (commerce escrow) | Commerce / marketplace APIs — **not** CRM entities |
| Support thread (`psup_*`) | Messenger / support plane — timeline shown read-only on Contact 360, not a CRM row |

Lead capture: Integration Hub recipe **`recipe_crm_lead_capture`** upserts contacts from webhooks. Optional deal creation is **deferred** post-v1.

## UI

Dual-shell routes: `/user/projects/:projectId/crm` and `/dev/projects/:projectId/crm`.

## Further reading

- [MCP capability matrix](MCP_CAPABILITY_MATRIX.md) — `crm.*` section
- [Agents ownership](agents/OWNERSHIP_AND_SCOPES.md) — same project-scoped pattern
