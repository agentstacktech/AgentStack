# Support — integration quickstart

```typescript
import { sdk } from "@agentstack/sdk";

// Example: fetch configuration / thread handles — method names follow your SDK semver.
const cfg = await sdk.support.getConfig({ projectId });
```

REST highlights (authorize as usual):

```http
GET /api/support/config?project_id=<uuid>
PATCH /api/support/config
```

Full paths — **OpenAPI** ([OPENAPI.md](../OPENAPI.md)).

## MCP

Category **`social.support.*`** in [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) lists transitions and inbox operations.

**Next:** [AI_HANDOFF.md](AI_HANDOFF.md) · [../tutorials/05_support_channel.md](../tutorials/05_support_channel.md) · [../api/support.md](../api/support.md)
