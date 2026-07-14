# Storefront Studio — bulk product fill and vitrine publish

**Platform line:** 0.4.14+ · **Genetic tags:** `core.commerce.storefront_seed.gen1`, `frontend.commerce.storefront_studio.gen1`

Storefront Studio is the low-input path from product name → catalog rows → hosted vitrine, orchestrated by **`StorefrontSeedService`**.

## UI entry

Open Assets workspace with studio mode:

- `/user/projects/:projectId/assets?mode=studio`
- `/dev/projects/:projectId/assets?mode=studio`

Discovery hub and sell playbooks deep-link here for bulk fill.

## Saga (plan → apply → undo)

1. **Plan** — preview rows, AI-assisted copy, hosting target
2. **Apply** — writes asset catalog + optional hosted publish (per-project lock)
3. **Undo** — reverses last seed run via audit run id

## MCP

Domain **`commerce.storefront.*`** — plan, apply, undo, status. See [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md).

## Hosted vitrine

Publishing uses the hosted storefront bridge. After publish, hosting-plane cache invalidates so `/s/{projectId}/{slug}` reflects new catalog.

Demo vitrine: `/s/2/demo-store/` (when demo project is seeded).

## SDK / CDN

Hosted sites load commerce ESM from the platform CDN — see [sdk/README.md](sdk/README.md) and [VERSIONING.md](VERSIONING.md) for the current patch segment.

## Boundaries

- **Assets wizard** — single-product compose; Studio is **bulk** orchestration
- **Marketplace checkout** — separate commerce contour; checkout→CRM contact recipe is backlog post-v1
