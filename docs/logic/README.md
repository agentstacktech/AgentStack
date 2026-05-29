# Logic and Rules Engine

**Rules** react to data events; **protein commands** handle DNA CRUD; **webhooks** integrate externally. Developer shell: `/dev/projects/:projectId/logic`.

| Mechanism | Use when |
|-----------|----------|
| Logic rules | Event-driven automation inside project |
| `sdk.protocol.executeCommand` | DNA mutations with cache invalidation |
| Integration recipes | External triggers — [integrations/](../integrations/) |

**MCP:** `logic.*`, `scheduler.*` (cron tasks)

```typescript
// Illustrative — see OpenAPI for full scheduler params
await sdk.scheduler.createTask({ projectId: 42, cron: '0 8 * * *', /* task payload */ });
```

**Next:** [../PARITY_MATRIX.md](../PARITY_MATRIX.md) · [../api/logic.md](../api/logic.md) · [../MCP_FLOWS_AND_SYNERGIES.md](../MCP_FLOWS_AND_SYNERGIES.md)
