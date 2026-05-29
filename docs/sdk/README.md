# JavaScript SDK surfaces (`@agentstack/sdk`)

Canonical package: **`@agentstack/sdk`** (TypeScript). Major entry points:

| Facade | Scope |
|--------|--------|
| **`sdk.protocol`** | REST + 8DNA + `/commands/*` + snapshots — unified integration plane |
| **`sdk.platform`** | Stable “AI surface” for assistants — [PLATFORM_SURFACE.md](PLATFORM_SURFACE.md) |
| **`sdk.protocol`** | Commands, snapshots, REST helpers — [AGENT_PROTOCOL_QUICKSTART.md](AGENT_PROTOCOL_QUICKSTART.md) |
| **`sdk.messenger`** | Messenger threads — parity with MCP social actions |
| **`sdk.agents`** | Agents Fleet CRUD + runs |
| **`sdk.support`** | Project support REST helpers |
| **`sdk.integrations`** | Integration Hub connections and recipes |
| **`sdk.hosting`** | Static sites and SPA deploy |
| **`sdk.commerce`** | Shop, listings, storefront helpers |
| **`sdk.storage`** | Files, quotas, uploads |
| **`sdk.media.*`** | Thumbnails, OPFS, resumable uploads, lightbox, audio capture, photo ladder |
| **`sdk.mobile`** | Mobile-oriented helpers — [MOBILE_AND_PWA.md](MOBILE_AND_PWA.md) |

## Quick install

```bash
npm install @agentstack/sdk
```

Wire authentication once (Bearer from your OAuth/API key flow).

## Further reading

- [BUILD_YOUR_PRODUCT.md](../BUILD_YOUR_PRODUCT.md) — choose your integration path
- [RECIPES.md](RECIPES.md) — copy-paste tutorials (hosting, CRUD, commerce, …)
- [storage/](../storage/) · [messenger/](../messenger/) — domain guides
- [MEDIA_V2_DENOISE_AND_COMPRESS.md](MEDIA_V2_DENOISE_AND_COMPRESS.md)
- [REACT_QUERY.md](REACT_QUERY.md) · [ERROR_HANDLING.md](ERROR_HANDLING.md)
