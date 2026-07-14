# CRM REST & MCP reference

Project-scoped CRM tissue for contacts, deals, pipelines, and activities. All data lives in the unified project store (8DNA); CRM is not a separate product database.

**REST base:** `/api/projects/{project_id}/crm/*`  
**MCP category:** `crm` (live count in [MCP_CAPABILITY_MATRIX.md](../../MCP_CAPABILITY_MATRIX.md) § crm)  
**API key service cap:** `crm`  
**OpenAPI:** [Swagger](https://agentstack.tech/swagger)

---

## Entity types (6)

| Type | Purpose | Integrator notes |
|------|---------|------------------|
| `crm_contact` | People and leads | Lifecycle: `lead` → `mql` → `sql` → `customer` → `churned` |
| `crm_deal` | Pipeline opportunities | Stage moves use optimistic concurrency (`rev` / ETag) |
| `crm_pipeline` | Stage definitions | Default pipeline created on first use |
| `crm_activity` | Notes, calls, tasks, meetings | Kinds: `call`, `email`, `meeting`, `note`, `task`, `stage_change` |
| `crm_company` | Account records | **Schema-only roadmap** — types exist; full company CRUD UI/API expansion is planned |
| `crm_segment` | Audience criteria | **Schema-only roadmap** — criteria model reserved; list/filter UX not yet exposed |

Deal fields `linked_order_id` and `linked_checkout_session_id` are **read-only** from commerce until the CRM–commerce bridge writes them automatically.

---

## MCP actions (17)

| Action | Summary |
|--------|---------|
| `crm.list_contacts` | Paginated contact list (`page`, `per_page`, `q`, `lifecycle`) |
| `crm.upsert_contact` | Create or update contact (email dedupe) |
| `crm.create_deal` | New deal in default pipeline |
| `crm.move_deal_stage` | Move deal; optional `expected_rev` for concurrency |
| `crm.log_activity` | Log note, call, task, etc. |
| `crm.search` | Full-text contact search |
| `crm.get_contact_360` | Profile + deals + unified timeline |
| `crm.magic_fill` | AI/heuristic quick-create (dry-run, no write) |
| `crm.suggest_field` | Field suggestions from partial context |
| `crm.import_contacts` | Batch import with email dedupe |
| `crm.export_contact` | GDPR export bundle |
| `crm.erase_contact` | GDPR anonymize/erase |
| `crm.list_board` | Kanban board (pipeline columns + deals) |
| `crm.get_deal_timeline` | Deal-scoped activity timeline |
| `crm.get_crm_config` | Saved views and settings blob |
| `crm.patch_crm_config` | Patch config (writers: `saved_views` only) |
| `crm.merge_contacts` | Merge duplicate into primary |

Full matrix row: [MCP_CAPABILITY_MATRIX.md](../../MCP_CAPABILITY_MATRIX.md) § crm.

---

## REST endpoints

All routes require authentication. Mutations require project `write` or `admin`. Reads require project `read`.

| Method | Path | Notes |
|--------|------|-------|
| `GET` | `/contacts` | Query: `page`, `per_page` (≤200), `q`, `lifecycle`, `owner_user_id` |
| `POST` | `/contacts` | Create contact |
| `GET` | `/contacts/{contact_id}/360` | 360 view |
| `GET` | `/contacts/{contact_id}/export` | GDPR export |
| `POST` | `/contacts/{contact_id}/erase` | GDPR erase |
| `POST` | `/contacts/import` | Body: `{ "contacts": [...], "source": "import" }` — **max 500 rows** per request |
| `POST` | `/contacts/merge` | Body: `{ "primary_id", "duplicate_id" }` |
| `GET` | `/board` | Optional `pipeline_id` |
| `POST` | `/deals` | Create deal |
| `PATCH` | `/deals/{deal_id}/stage` | Body: `{ "stage_id", "expected_rev"? }`; header `If-Match: "{rev}"` |
| `GET` | `/deals/{deal_id}/timeline` | Deal timeline |
| `POST` | `/activities` | Log activity |
| `GET` | `/search` | Query: `q`, `limit` (≤200) |
| `GET` / `PATCH` | `/config` | Project CRM config |
| `POST` | `/ai/magic-fill` | REST mirror of `crm.magic_fill` |
| `POST` | `/ai/suggest-field` | REST mirror of `crm.suggest_field` |

Prefix every path with `/api/projects/{project_id}/crm`.

---

## Lifecycle stages

Contact `lifecycle` values (ordered funnel):

```
lead → mql → sql → customer → churned
```

Set on create/update via contact payload or filter lists with `?lifecycle=mql`.

---

## Concurrency (ETag / 409)

Deal stage moves are optimistic. Each deal carries a monotonic `rev`.

1. Read deal (board or 360) and note `rev`.
2. Send `PATCH .../deals/{deal_id}/stage` with `"expected_rev": <rev>` **or** header `If-Match: "<rev>"`.
3. On conflict the API returns **409** with `{ "message", "current" }` — refresh and retry.

Response may include `ETag: "<rev>"` on successful stage move.

---

## Limits

| Limit | Value |
|-------|-------|
| `per_page` (list contacts) | 1–200 (default 50) |
| `limit` (search) | 1–200 (default 50) |
| `contacts` per import | **500** (additional rows truncated server-side) |

Rate limits follow [operations-public/RATE_LIMITS_AND_ERRORS.md](../../operations-public/RATE_LIMITS_AND_ERRORS.md).

---

## RBAC & API keys

**Project permissions** (mapped to CRM capabilities):

| Capability | Required project permission |
|------------|----------------------------|
| `crm:read` | `read` |
| `crm:write` | `write` |
| `crm:export` | `write` |
| `crm:admin` | `admin` |

Non-admin users may only see records they own (`owner_user_id` match) on list endpoints.

**API keys:** include service cap **`crm`** on the key (see [auth/API_KEYS_AND_SCOPES.md](../../auth/API_KEYS_AND_SCOPES.md)).

---

## Related docs

- [how-to/crm/INTEGRATION_QUICKSTART.md](../../how-to/crm/INTEGRATION_QUICKSTART.md)
- [explanation/crm/CRM_AS_8DNA_TISSUE.md](../../explanation/crm/CRM_AS_8DNA_TISSUE.md)
- [crm/README.md](../../crm/README.md)

---

## Cross-system synergies

| Partner tissue | Link | Status |
|----------------|------|--------|
| **Integrations** | Inbound `lead_capture` recipes; outbound on `crm.deal.*` events | Solid for CRM events; commerce outbound via logic — see cookbook |
| **Support** | `linked_user_id` on contact → 360 includes support timeline when bound | Solid when user linked |
| **Commerce** | `deal.linked_order_id` | **Read-only** until order-completed bridge writes the field |
| **Agents + RAG** | `agents.sdr-run`, `rag.ingest`, `crm.magic_fill` / `crm.suggest_field` | Solid for AI-assisted CRM |
| **Search RBAC** | `crm.search` MCP | **Caveat:** not role-safe until routed through application service |

Recipes: [webhook-crm-support.md](../../explanation/synergies/webhook-crm-support.md) · [SYNERGY_COOKBOOK.md](../../explanation/synergies/SYNERGY_COOKBOOK.md)

