# Assets wizard & card studio

The **assets catalog** holds currencies, items, cards, and marketplace product specs inside project DNA. Two builder surfaces matter for integrators:

1. **Preset wizard** — pick a template, fill `inputSchema`, create via MCP.
2. **Card studio** — visual compose for trading-card style assets (dashboard module).

---

## SDK entry

```typescript
import {
  listPresets,
  composeFromPreset,
  type AssetPreset,
} from "@agentstack/sdk/commerce/assets";
```

The same types are re-exported from `@agentstack/sdk` commerce façade.

---

## MCP actions

| Action | Purpose |
|--------|---------|
| `assets.list_presets` | Wizard catalog (`seedComponents`, `inputSchema`) |
| `assets.create` | Create asset in `project.data.assets.items[]` |
| `assets.list` | List all assets |
| `assets.get` | Fetch one asset |
| `assets.update` | Patch asset |
| `assets.delete` | Remove asset |

**Service pattern:** list presets → validate answers → `assets.create`.

---

## Example: create from preset

```json
{
  "action": "assets.list_presets",
  "params": { "project_id": 42 }
}
```

```json
{
  "action": "assets.create",
  "params": {
    "project_id": 42,
    "type": "product",
    "name": "Starter Pack",
    "seedComponents": ["title", "price", "image"],
    "answers": {
      "title": "Starter Pack",
      "price": "19.99",
      "currency": "USDT"
    }
  }
}
```

---

## REST

Topic guide: [api/assets.md](../api/assets.md). Commerce listings consume catalog assets via Storefront Studio — [STOREFRONT_STUDIO.md](../commerce/STOREFRONT_STUDIO.md).

---

## Card studio (UI)

Dashboard **Assets** workspace includes card compose previews. Automation parity is via `assets.create` with card-oriented presets — not a separate MCP namespace.

---

## Currencies

Project currencies are assets with `type: "currency"`. Create/update through `assets.*` before wallet or marketplace flows.

**Synergy:** [static-storefront-commerce.md](../explanation/synergies/static-storefront-commerce.md)

**Next:** [commerce/STOREFRONT_STUDIO.md](../commerce/STOREFRONT_STUDIO.md) · [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) § assets
