# JavaScript SDK surfaces (`@agentstack/sdk`)

Canonical package: **`@agentstack/sdk`** (TypeScript). Major entry points:

| Facade | Scope |
|--------|--------|
| **`sdk.protocol`** | REST + 8DNA + `/commands/*` + snapshots — unified integration plane |
| **`sdk.platform`** | Stable “AI surface” subset for assistants ([SDK_AI_SURFACE.md](https://github.com/agentstacktech/AgentStack/blob/master/docs/SDK_AI_SURFACE.md)) |
| **`sdk.messenger`** | Messenger threads — parity with MCP social actions |
| **`sdk.agents`** | Agents Fleet CRUD + runs |
| **`sdk.support`** | Project support REST helpers |
| **`sdk.media.*`** | Thumbnails, OPFS, resumable uploads, lightbox, audio capture, photo ladder |

## Quick install

```bash
npm install @agentstack/sdk
```

Wire authentication once (Bearer from your OAuth/API key flow).

## Further reading

- [storage/](../storage/) — media primitives
- [messenger/](../messenger/) — chat facade
- [MEDIA_V2_DENOISE_AND_COMPRESS.md](MEDIA_V2_DENOISE_AND_COMPRESS.md)
