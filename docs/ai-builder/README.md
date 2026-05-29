# AI Builder (public integrator view)

Hosted **mini-apps** use a **Unified Application Manifest (UAM v1)** — structure is validated; LLM fills copy and custom slots.

**MCP actions:** `ai_builder.manifest.get`, `ai_builder.manifest.validate`, `ai_builder.compose.preview`

```typescript
import { validateAppManifest } from '@agentstack/sdk';

const manifest = validateAppManifest({
  manifest_version: '1',
  app_id: 'demo',
  name: 'Demo',
  version: '1.0.0',
  routes: [{ path: '/', module_id: 'page', props: {} }],
  modules: ['page'],
  capabilities: [],
});
```

**Hosting:** publish output via [hosting/HOSTING_QUICKSTART.md](../hosting/HOSTING_QUICKSTART.md). On-platform visual editing may evolve — [ROADMAP_INTEGRATOR.md](../ROADMAP_INTEGRATOR.md).

**Next:** [../api/ai-builder.md](../api/ai-builder.md)
