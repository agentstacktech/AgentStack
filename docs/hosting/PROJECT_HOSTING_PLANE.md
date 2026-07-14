# Project hosting plane

The **hosting plane** is the project-level summary of **Host → Sell → Scale** progress: sites, storefront readiness, and recommended next actions.

---

## Ladder stages

| Stage | Meaning | Typical signals |
|-------|---------|-----------------|
| **Host** | At least one published site or SPA | `/s/...` or `/a/...` URL live |
| **Sell** | Commerce vitrine or listings active | Storefront manifest, shop index rows |
| **Scale** | Releases, edge health, multi-site ops | Snapshots, promote history, AGNT/compute funding |

The dashboard **project overview** tile and hosting hub read the same plane payload.

---

## MCP: `hosting.project.status`

```json
{
  "action": "hosting.project.status",
  "params": { "project_id": 42 }
}
```

Response shape (integrator-facing):

| Field | Content |
|-------|---------|
| `ladder` | `host`, `sell`, `scale` substates and completion hints |
| `sites` | Bucket list, publish flags, public URLs |
| `next_actions` | Ranked suggestions (publish, seed storefront, edge health, …) |

Pair with Compass playbook `host-and-sell-15` for guided Host→Sell.

---

## REST contours

Hosting file operations use `/api/projects/{project_id}/hosting/*` and Sites UI BFF routes. Plane status is **MCP-first**; SDK wrappers may expose `getProjectPlane` helpers — check your SDK semver.

---

## Related MCP

| Action | When |
|--------|------|
| `hosting.site.edge_health` | 404 or stale edge after publish |
| `hosting.demo_store.status` | Compare against `/s/2/demo-store/` promo |
| `commerce.storefront.hosted_manifest` | Sell-stage merchandising state |

---

## Honest caveats

- Ladder **Sell** may show partial completion when only assets exist without listings — run Storefront Studio apply.
- **Scale** metrics depend on subscription tier (release retention, storage pool).
- Plane cache may lag seconds after publish — UI invalidates on successful publish; agents should retry `hosting.project.status`.

**Next:** [HOSTING_GOLDEN_PATHS.md](HOSTING_GOLDEN_PATHS.md) · [commerce/STOREFRONT_STUDIO.md](../commerce/STOREFRONT_STUDIO.md)
