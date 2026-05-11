# Storage — client-side media & files

Use **`sdk.media.*`** from `@agentstack/sdk` for thumbnails, OPFS cache, resumable uploads, and optional lightbox — same primitives across messenger, dashboard storage module, and custom apps.

| Doc | Purpose |
|-----|---------|
| [INTEGRATION_QUICKSTART.md](INTEGRATION_QUICKSTART.md) | Thumbnail + OPFS + resumable upload |
| [LIGHTBOX_GUIDE.md](LIGHTBOX_GUIDE.md) | Headless PhotoSwipe adapter |

REST uploads use **tus-lite** semantics on **`PATCH /api/files/resumable/{op_id}`** after creating an upload session — see [OPENAPI.md](../OPENAPI.md) / Swagger **Files** tags.
