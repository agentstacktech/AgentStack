# Storage — integration quickstart

```typescript
import { sdk } from "@agentstack/sdk";

// Thumbnail + cache (feature-detect in production apps)
const thumb = await sdk.media.thumbnail.generate(fileOrBlob, { maxEdge: 256 });

// Persist bytes in OPFS KV when available
await sdk.media.opfs.put(cacheKey, processedBlob);

// Resumable upload loop (create session via REST first — see OpenAPI)
await sdk.media.resumable.uploadChunk(opId, slice, { offset });
```

**Requirements:** modern Chromium/WebKit where you ship; provide fallback to direct upload URLs when OPFS or workers are unavailable.

## Headers

Upload finalize endpoints accept routing hints as documented in OpenAPI (folder/target headers). Use the exact names from **`openapi.json`** for your environment.
