# Hosted storefront

AgentStack serves **tenant storefront vitrines** from the hosting plane — static bundles with commerce boot metadata.

---

## Demo URL

Public promo vitrine (sandbox catalog):

**https://agentstack.tech/s/2/demo-store/**

Inspect layout, cart drawer, and checkout bridge without provisioning your own bucket first.

MCP health: `hosting.demo_store.status` — catalog row count, published build id, manifest bucket.

---

## Your project URL pattern

After publish:

```
https://agentstack.tech/s/{project_id}/{bucket_name}/
```

SPA client-router storefronts may use `/a/{project_id}/{bucket_name}/` when configured.

Create and publish via **Storage → Sites** or MCP `hosting.site.quick_start` + `commerce.storefront.hosted_publish`.

---

## SDK CDN (commerce ESM)

Embed commerce client code without bundling the full SDK:

```
https://agentstack.tech/sdk/v{version}/commerce.esm.js
```

Example (version from your deployment):

```html
<script type="importmap">
{
  "imports": {
  "@agentstack/commerce": "/sdk/v0.4.18/commerce.esm.js"
  }
}
</script>
```

Build pipeline publishes `commerce.esm.js` with Brotli/gzip at the edge. Keep version pinned in production.

---

## MCP publish flow

```json
{
  "action": "commerce.storefront.hosted_manifest",
  "params": { "project_id": 42 }
}
```

```json
{
  "action": "commerce.storefront.hosted_publish",
  "params": { "project_id": 42 }
}
```

---

## Checkout bridge

Guest cart on `/s/...` deeplinks to platform checkout when the user signs in — see [commerce/WALLET_AND_PAYMENTS.md](WALLET_AND_PAYMENTS.md).

---

## Caveats

- **Edge readiness:** if `/s/...` 404s immediately after publish, call `hosting.site.edge_health` or republish from Sites UI.
- **SEO:** tenant meta via Storage → Sites → SEO and `GET /api/seo/meta`.
- **CRM link:** deal `linked_order_id` is read-only until commerce–CRM bridge automation lands.

**Next:** [STOREFRONT_STUDIO.md](STOREFRONT_STUDIO.md) · [hosting/HOSTING_QUICKSTART.md](../hosting/HOSTING_QUICKSTART.md)
