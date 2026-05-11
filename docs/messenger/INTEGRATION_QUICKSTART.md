# Messenger — integration quickstart

Use **`@agentstack/sdk`** after normal authentication (same Bearer token you use for REST).

```typescript
import { sdk } from "@agentstack/sdk";

// Resolve a channel reference from your app context (DM example).
const ref = { kind: "dm" as const, channelId: "<uuid-from-your-app>" };

const live = sdk.messenger.channel(ref).messages.live({
  onSnapshot: (thread) => {
    console.log("hydrated thread", thread.messages?.length ?? 0);
  },
  onAppend: (batch) => {
    console.log("delta batch", batch.length);
  },
});

// Later: unsubscribe when your React component unmounts.
void live;
```

**React pattern:** wrap in `useEffect`, capture unsubscribe/cleanup returned by your SDK version’s subscription helper.

## MCP parity for AI agents

Tool loops call the same capabilities as humans via MCP (e.g. **`social.chat.delta`**, CRDT-related actions where enabled). See [SOCIAL_API_REFERENCE.md](SOCIAL_API_REFERENCE.md).

## Next steps

- [OFFLINE_AND_RELIABILITY.md](OFFLINE_AND_RELIABILITY.md) — retries, ordering guarantees at product level.
- [../api/messenger.md](../api/messenger.md) — REST endpoints that complement MCP.
