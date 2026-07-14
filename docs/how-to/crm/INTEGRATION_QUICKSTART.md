# CRM — integration quickstart

End-to-end: **contact → deal → move stage → 360 view**. Uses real action names and REST paths.

**Prerequisites:** project ID, session cookie or API key with service cap `crm`, project `write` permission.

> **Docs site:** Starlight renders curl / SDK / MCP as tabs — see [CODE_TABS convention](../../reference/CODE_TABS.md) on the docs site mirror.

---

## 1. Create a contact

### REST (curl)

```bash
curl -sS -X POST "https://agentstack.tech/api/projects/42/crm/contacts" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "Alex Rivera",
    "email": "alex@example.com",
    "source": "api",
    "custom": { "lifecycle": "lead" }
  }'
```

Save `contact.id` from the response.

### MCP

```json
{
  "action": "crm.upsert_contact",
  "params": {
    "project_id": 42,
    "contact": {
      "display_name": "Alex Rivera",
      "emails": ["alex@example.com"],
      "lifecycle": "lead",
      "source": "mcp"
    }
  }
}
```

### SDK

```typescript
import { sdk } from "@agentstack/sdk";

const { contact } = await sdk.protocol.execute({
  action: "crm.upsert_contact",
  params: {
    project_id: 42,
    contact: {
      display_name: "Alex Rivera",
      emails: ["alex@example.com"],
      lifecycle: "lead",
    },
  },
});
const contactId = contact.id;
```

---

## 2. Create a deal linked to the contact

### REST

```bash
curl -sS -X POST "https://agentstack.tech/api/projects/42/crm/deals" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Starter plan — Alex",
    "contact_ids": ["CONTACT_ID_HERE"],
    "amount": "1200"
  }'
```

### MCP

```json
{
  "action": "crm.create_deal",
  "params": {
    "project_id": 42,
    "deal": {
      "title": "Starter plan — Alex",
      "contact_ids": ["CONTACT_ID_HERE"],
      "amount": "1200"
    }
  }
}
```

Note `deal.id`, `deal.stage_id`, and `deal.rev`.

---

## 3. Move deal stage (with concurrency)

List the board to discover target `stage_id` values:

```bash
curl -sS "https://agentstack.tech/api/projects/42/crm/board" \
  -H "Authorization: Bearer $TOKEN"
```

Move stage (pass `expected_rev` from the deal row):

```bash
curl -sS -X PATCH "https://agentstack.tech/api/projects/42/crm/deals/DEAL_ID/stage" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -H 'If-Match: "3"' \
  -d '{ "stage_id": "qualified" }'
```

### MCP

```json
{
  "action": "crm.move_deal_stage",
  "params": {
    "project_id": 42,
    "deal_id": "DEAL_ID_HERE",
    "stage_id": "qualified",
    "expected_rev": 3
  }
}
```

On **409**, re-fetch the deal and retry with the new `rev`.

---

## 4. Contact 360 view

### REST

```bash
curl -sS "https://agentstack.tech/api/projects/42/crm/contacts/CONTACT_ID/360" \
  -H "Authorization: Bearer $TOKEN"
```

### MCP

```json
{
  "action": "crm.get_contact_360",
  "params": {
    "project_id": 42,
    "contact_id": "CONTACT_ID_HERE"
  }
}
```

Response includes `contact`, open `deals`, and a unified `timeline` (activities + stage changes).

---

## Optional: log an activity

```json
{
  "action": "crm.log_activity",
  "params": {
    "project_id": 42,
    "activity": {
      "kind": "note",
      "body": "Demo completed — moving to qualified.",
      "contact_id": "CONTACT_ID_HERE",
      "deal_id": "DEAL_ID_HERE"
    }
  }
}
```

---

## MCP transport

Post to `https://agentstack.tech/mcp` with `agentstack.execute` — see [MCP_QUICKSTART.md](../../MCP_QUICKSTART.md).

**Next:** [reference/api/crm.md](../../reference/api/crm.md) · [explanation/crm/CRM_AS_8DNA_TISSUE.md](../../explanation/crm/CRM_AS_8DNA_TISSUE.md)
