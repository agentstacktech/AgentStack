# React Query + SDK

Use **`@agentstack/react`** hooks so server state stays in TanStack Query — not ad-hoc `useState` + `useEffect`.

```typescript
import { useSDKQuery } from '@agentstack/react';

const { data, isLoading } = useSDKQuery(
  ['agents', projectId],
  () => sdk.agents.list({ projectId }),
  { staleTime: 60_000 }
);
```

After mutations, invalidate related keys (or use `useSDKMutation` helpers from the same package).

**Next:** [ERROR_HANDLING.md](ERROR_HANDLING.md)
