# Storefront Studio

**Storefront Studio** is the commerce workspace for seeding catalog assets, listings, and hosted vitrine bundles from one place.

**UI entry:** project commerce / storefront routes with **`?mode=studio`** (optional `studioTab=bulk` for bulk ingest).

---

## What Studio does

1. **Plan** — dry-run asset + listing drafts (`commerce.storefront.seed_plan`).
2. **Ingest** — parse CSV, JSON, AI batch, or clone sources (`commerce.storefront.seed_ingest`).
3. **Apply** — upsert assets, create listings, refresh storefront index (`commerce.storefront.seed_apply`).
4. **Undo** — cancel listings from a prior run (`commerce.storefront.seed_undo`).
5. **One-click fill** — listings for eligible catalog assets (`commerce.storefront.one_click_fill`).
6. **Hosted publish** — push static vitrine + boot JSON (`commerce.storefront.hosted_publish`).

---

## MCP actions (`commerce.storefront.*`)

| Action | REST mirror |
|--------|-------------|
| `commerce.storefront.seed_plan` | `POST /api/commerce/storefront/seed/plan` |
| `commerce.storefront.seed_ingest` | `POST /api/commerce/storefront/seed/ingest` |
| `commerce.storefront.seed_apply` | `POST /api/commerce/storefront/seed/apply` |
| `commerce.storefront.seed_undo` | `POST /api/commerce/storefront/seed/undo` |
| `commerce.storefront.one_click_fill` | `POST /api/commerce/storefront/seed/one-click-fill` |
| `commerce.storefront.hosted_manifest` | `GET /api/commerce/storefront/hosted/manifest` |
| `commerce.storefront.hosted_publish` | `POST /api/commerce/storefront/hosted/publish` |

---

## Example: plan then apply

```json
{
  "action": "commerce.storefront.seed_plan",
  "params": {
    "project_id": 42,
    "source": "ai_batch",
    "items": [{ "title": "Demo SKU", "price": "9.99" }]
  }
}
```

Review the returned plan, then:

```json
{
  "action": "commerce.storefront.seed_apply",
  "params": {
    "project_id": 42,
    "plan": { "...": "from seed_plan response" }
  }
}
```

---

## SDK & UI

- Dashboard: **Storefront Studio** under project commerce (`?mode=studio`).
- After seeding, use [HOSTED_STOREFRONT.md](HOSTED_STOREFRONT.md) for public `/s/...` vitrine URLs.

**Synergy:** [static-storefront-commerce.md](../explanation/synergies/static-storefront-commerce.md)

**Next:** [HOSTED_STOREFRONT.md](HOSTED_STOREFRONT.md) · [assets/ASSETS_WIZARD_AND_CARD_STUDIO.md](../assets/ASSETS_WIZARD_AND_CARD_STUDIO.md)
