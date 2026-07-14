# Why CRM is tissue on the unified project store

AgentStack CRM is **not** a standalone SaaS product with its own database. It is **tissue**: structured entities stored in the same **8DNA project store** that already holds agents, commerce assets, logic rules, and hosting metadata.

---

## One store, many surfaces

Every CRM row is a typed entity in project DNA:

| Entity | Role |
|--------|------|
| `crm_contact` | People and lifecycle |
| `crm_deal` | Pipeline value |
| `crm_pipeline` | Stage definitions |
| `crm_activity` | Notes and tasks |
| `crm_company` | Accounts (schema reserved) |
| `crm_segment` | Audiences (schema reserved) |

The dashboard **CRM workspace**, REST `/api/projects/{id}/crm/*`, and MCP `crm.*` actions are **views and verbs** on that store — not separate backends.

---

## Benefits for integrators

1. **Single project scope** — One `project_id` for CRM, support, commerce, and agents; no cross-product API keys.
2. **Unified permissions** — Project RBAC (`read` / `write` / `admin`) maps to CRM capabilities; API keys use service cap `crm`.
3. **Agent-native** — Fleet agents call the same MCP actions (`crm.upsert_contact`, `crm.get_contact_360`) as your scripts.
4. **Timeline fusion** — Contact 360 can aggregate CRM activities with signals from other tissues as bridges mature (commerce order links are read-only until the bridge lands).
5. **Robot-ready** — Entities carry `rev` for optimistic concurrency; imports and exports align with GDPR flows without a second CRM vendor export.

---

## What CRM is not

- **Not** an isolated CRM SKU with separate billing or org hierarchy.
- **Not** a full company master or segment builder yet — `crm_company` and `crm_segment` types exist in schema; product surfaces are on the roadmap.
- **Not** the system of record for payments — deal `linked_order_id` is populated by commerce bridge work, not writable by arbitrary CRM PATCH today.

---

## Mental model

```
Project (8DNA)
 └── ecosystem.crm.* entities
      ├── contacts (lifecycle funnel)
      ├── deals (pipeline + rev)
      ├── activities (timeline)
      └── config (saved views)
```

Integrators should think **“CRM verbs on project data”** rather than **“CRM API product.”**

**See also:** [reference/api/crm.md](../../reference/api/crm.md) · [architecture/ROBOT_READY_8DNA.md](../../architecture/ROBOT_READY_8DNA.md)
