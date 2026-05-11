# Lightbox (`sdk.media.lightbox`)

The SDK exposes a **dynamic import** adapter around PhotoSwipe so **listing pages pay zero bundle cost** until a user opens a full-screen preview.

```typescript
const { openLightbox } = await import("@agentstack/sdk/media/lightbox");
await openLightbox({ items: [{ src: url, w: 1200, h: 800 }] });
```

Validate dimensions or use unknown placeholders — Virtuoso-friendly lists should not block on image decode.
