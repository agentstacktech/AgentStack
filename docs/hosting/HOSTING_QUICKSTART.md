# Hosting quick start (public)

**Product:** [agentstack.tech](https://agentstack.tech) · **Funnel:** [host-site](https://agentstack.tech/host-site)

Publish static sites and SPA builds on AgentStack without a separate static host for your MVP.

---

## What you get

| Item | Detail |
|------|--------|
| Static sites | Public URLs at `/s/{projectId}/{bucketName}/` |
| SPA builds | Client-router apps at `/a/{projectId}/{bucketName}/` |
| Upload | Paste HTML, upload files, or deploy a ZIP (UI or MCP) |
| Releases | Snapshot and promote prior versions (rollback) |
| Files | Project Storage pairs with Sites; site bytes count toward your plan storage pool |

---

## UI path (developer shell)

1. Sign in at [agentstack.tech](https://agentstack.tech).
2. Open **Storage → Sites** for your project (or start at [/host-site](https://agentstack.tech/host-site) before sign-in).
3. Create a site: paste HTML, drop a ZIP, or upload files.
4. **Publish** when ready; copy the live `/s/...` URL from the site card.
5. Use **Releases** in the site drawer to snapshot or promote a previous version.

---

## MCP path (agents and automation)

Discovery:

```http
GET https://agentstack.tech/mcp/actions
```

Quick publish (example action):

```
hosting.site.quick_start
```

Batch upload:

```
hosting.deploy_files
```

Authenticate with OAuth Bearer or `X-API-Key` on `POST https://agentstack.tech/mcp` using `agentstack.execute` — see [MCP_QUICKSTART.md](../MCP_QUICKSTART.md).

Full action list: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

---

## Honest limits

- Site files count toward the **project owner's storage quota** (subscription tier).
- Multiple sites per project are supported within plan limits.
- If a public URL is not reachable immediately, **Republish** from the Sites UI (edge readiness).
- On-platform AI editing of hosted sites is **in development** — [ROADMAP_INTEGRATOR.md](../ROADMAP_INTEGRATOR.md).

Pricing: [agentstack.tech/pricing](https://agentstack.tech/pricing) · Tiers: [subscription/SUBSCRIPTION_TIERS.md](../subscription/SUBSCRIPTION_TIERS.md).

---

## Related docs

- [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md)
- [storage/README.md](../storage/README.md) (files and hub)
- [tutorials/01_hosting_static_site.md](../tutorials/01_hosting_static_site.md)

**Next:** [../BUILD_YOUR_PRODUCT.md](../BUILD_YOUR_PRODUCT.md)
