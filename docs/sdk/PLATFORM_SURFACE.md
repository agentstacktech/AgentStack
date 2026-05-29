# SDK platform surface (`sdk.platform`)

Stable slice of `@agentstack/sdk` for assistants and integrations. Full SDK remains on `AgentStackSDK`; use **`sdk.platform`** when you want predictable names.

## When to use

| Situation | Use |
|-----------|-----|
| Login, REST, DNA, commands, snapshots | `sdk.platform.*` |
| Ecosystem facade (REST + commands + cache) | `sdk.platform.protocol` or `sdk.protocol` |
| Commerce, hosting, deep admin | Root `sdk.commerce`, `sdk.hosting` (see [README.md](README.md)) |

## Capability matrix for agents

```typescript
const matrix = await sdk.platform.getCapabilityMatrix();
// Align MCP action ids with matrix.actions for tool loops
```

## Surface map

| Area | `sdk.platform` field | Typical task |
|------|----------------------|--------------|
| HTTP / auth | `auth`, `http` | Login, tokens |
| REST | `api` | Projects, users |
| 8DNA tables | `dna`, `projects`, `users` | KV rows |
| Command bus | `protocol`, `proteinCommandChannel` | DNA CRUD commands |
| Rules | `command` | Rules Engine |
| Snapshots | `snapshots` + `protocol.invalidate*` | Client cache |
| Messenger | `social` | Chat REST |
| Support | `support` | Project support API |
| Storage | `storage` | Files and quota |
| Web Push | `webPush` | Browser subscriptions |
| RBAC | `rbac` | Roles |

**npm package:** [@agentstack/sdk](https://www.npmjs.com/package/@agentstack/sdk) · mirror repo [agentstack-sdk](https://github.com/agentstacktech/agentstack-sdk)

**Next:** [AGENT_PROTOCOL_QUICKSTART.md](AGENT_PROTOCOL_QUICKSTART.md) · [RECIPES.md](RECIPES.md)
