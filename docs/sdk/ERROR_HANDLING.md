# Error handling

| HTTP | Meaning | Action |
|------|---------|--------|
| 401 | Auth expired | Re-login or refresh token |
| 403 | RBAC / service_caps | Check role and API key scopes |
| 429 | Plan limit | [subscription/SUBSCRIPTION_TIERS.md](../subscription/SUBSCRIPTION_TIERS.md) |

```typescript
import { AgentStackError } from '@agentstack/sdk';

try {
  await sdk.platform.api.getProjects();
} catch (e) {
  if (e instanceof AgentStackError && e.status === 429) {
    // backoff and retry
  }
}
```

MCP async jobs: use `idempotency_key` in `options` — [MCP_OVERVIEW.md](../MCP_OVERVIEW.md).
