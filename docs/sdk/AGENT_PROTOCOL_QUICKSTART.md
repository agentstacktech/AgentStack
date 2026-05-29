# AgentProtocol quickstart

Use **`sdk.protocol`** on `AgentStackSDK` as the single entry for REST helpers, protein commands, and snapshot cache.

## 1. Read-through cache

```typescript
const data = await sdk.protocol.readThroughSnapshot(
  snapshotKeyProjectData(projectId),
  async () => {
    const res = await sdk.api.getProjectData(projectId);
    return {
      data: res.data,
      meta: { fetchedAt: Date.now(), revision: String(res.etag ?? res.timestamp) },
    };
  },
  { maxAgeMs: 60_000 }
);
```

## 2. Command bus + invalidate

```typescript
const result = await sdk.protocol.executeCommand({
  command_type: 'dna_crud',
  command_name: 'update_something',
  payload: {},
  priority: 5,
  timeout_ms: 30000,
  retry_count: 1,
});
if (result.success) {
  sdk.protocol.invalidateSnapshotPrefix(snapshotKeyPrefixProjectData());
}
```

## 3. Batch commands

```typescript
await sdk.protocol.executeCommandsBatch({ commands: [/* … */] });
```

## 4. Messenger

Prefer **`sdk.messenger`** or **`sdk.social`** for `/api/social/*` — `sdk.protocol` delegates when configured.

**Next:** [REACT_QUERY.md](REACT_QUERY.md) · [PARITY_MATRIX.md](../PARITY_MATRIX.md)
