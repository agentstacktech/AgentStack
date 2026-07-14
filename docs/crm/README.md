# CRM documentation hub

Project-scoped CRM for contacts, deals, pipelines, and activities — stored as **8DNA tissue**, not a separate product.

---

## Start here

| Doc | Audience | Time |
|-----|----------|------|
| [how-to/crm/INTEGRATION_QUICKSTART.md](../how-to/crm/INTEGRATION_QUICKSTART.md) | Integrators (curl, SDK, MCP) | ~10 min |
| [reference/api/crm.md](../reference/api/crm.md) | REST + MCP reference | Reference |
| [explanation/crm/CRM_AS_8DNA_TISSUE.md](../explanation/crm/CRM_AS_8DNA_TISSUE.md) | Why CRM lives on the project store | ~5 min |

---

## Quick facts

- **REST:** `/api/projects/{project_id}/crm/*`
- **MCP:** `crm.*` actions — see [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) § crm
- **API key cap:** `crm`
- **Lifecycle:** `lead` → `mql` → `sql` → `customer` → `churned`
- **Entities:** 6 types (companies/segments schema-only on roadmap)

---

## Synergies

- [webhook-crm-support.md](../explanation/synergies/webhook-crm-support.md) — inbound webhooks → CRM + support
- [ai-support-rag.md](../explanation/synergies/ai-support-rag.md) — support + RAG + agents
- [static-storefront-commerce.md](../explanation/synergies/static-storefront-commerce.md) — commerce (deal link roadmap)
- [SYNERGY_COOKBOOK.md](../explanation/synergies/SYNERGY_COOKBOOK.md) — full recipe index

**Integrations:** HubSpot/lead_capture via [integrations/](../integrations/) · **Commerce:** `linked_order_id` roadmap

---

## UI

Signed-in builders: **Developer shell → project → CRM** (`/dev/projects/:id/crm`).

**OpenAPI:** [agentstack.tech/swagger](https://agentstack.tech/swagger)
