# AgentStack — Product Build Flow for AI

**Gene:** `repo.plugins.product_flow.gen1` · **Machine index:** `docs/_generated/mcp_agent_instruction_index.json` → `product_archetypes` · **Plugin:** `provided_plugins/cursor-plugin/docs/PRODUCT_BUILD_FLOW.pointer.md`

**Purpose:** One narrative for building **sites, SaaS apps, stores, bots, and automations** on AgentStack. AgentStack **is** the backend — do not add Prisma, Express API routes, Stripe Checkout SDK, or a second auth stack for tenant apps.

---

## Premise

| Layer | Technology |
|-------|------------|
| Backend / data | MCP `agentstack.execute` + 8DNA (`projects.patch_data` leaf paths) |
| App transport | `@agentstack/sdk` — `sdk.platform.*`, `sdk.protocol`, domain facades |
| Auth (tenant app users) | `auth.*`, `rbac.*` — not NextAuth / Clerk |
| Hosting | `hosting.*` → live `/s/{project_id}/{bucket}/` |
| Automations | Logic Engine V2 (`logic.*`) + `scheduler.*` + `integrations.*` |
| AI | Agents Fleet, Bots, RAG, Knowledge mentor |

**Cursor plugin auth** (`/agentstack-authorize` or Connect G-A174) ≠ **app user auth** (`auth.login`).

---

## Mandatory bootstrap (every session)

| Phase | Goal | Typical actions |
|-------|------|-----------------|
| 1 Authenticate | MCP identity | Plugin OAuth, Device Code, or `auth.login` |
| 2 Project | Workspace scope | `projects.get_projects` → pick id |
| 3 Bind context | Tenant isolation | `context.project_id` on every batch (OAuth often defaults to `1`) |
| 4 Domain work | Feature actions | Recipe `mcp_session_setup` then domain playbook |

Prompt: `GET /mcp/prompts/get?name=agentstack_session_setup` · Command: `/agentstack-product-flow`

---

## Product archetypes (pick one first)

**Machine SoT:** `product_archetypes` in `mcp_agent_instruction_index.json` (autogen block in `agentstack-backend/SKILL.md`).

| Archetype | When | Start here |
|-----------|------|------------|
| `static_site` | landing, portfolio, HTML, ZIP | `mcp_hosting_quickstart` · `sdk.hosting.quickStart` |
| `saas` | users, roles, trials, dashboard | `saas_trial_system` · `/agentstack-scaffold-backend` |
| `ecommerce` | sell, storefront, digital goods | `mcp_hosting_quickstart` + `commerce.sell.activate` |
| `game` | player stats, IAP, mechanics | `card_game_basic` · `agentstack_use_case_game` prompt |
| `bot_channel` | Telegram, WhatsApp, bot studio | `mcp_bots_simulate` · Bot Studio |
| `backend_api` | CRUD API, webhooks | `mcp_session_setup` · `sdk.protocol` |
| `hosted_vertical_saas` | EDITFLOW, hosted workspace, kanban at `/s/` | `mcp_hosted_vertical_bootstrap` · prompt `agentstack_hosted_vertical` · `/agentstack-hosted-vertical` |
| `migrate_legacy` | Supabase, Firebase, Stripe cutover | `agentstack-migrator` agent |

**Live demos:** public [`/showcase`](https://agentstack.tech/showcase) gallery — reference only (do not run platform showcase bootstrap from tenant plugin):

| Showcase key | Archetype | Demo |
|--------------|-----------|------|
| `creator_portfolio` | `static_site` | Landing / portfolio |
| `helpdesk_bot` | `bot_channel` | Support bot |
| `collectibles_bazaar` | `ecommerce` | Digital goods storefront |
| `course_academy` | `saas` | Course / membership app |

Live catalog: [agentstack.tech/showcase](https://agentstack.tech/showcase).

---

## Channel ladder (prefer top-down)

1. **MCP** — `agentstack.execute` (IDE agents, Cursor plugin)
2. **SDK** — `@agentstack/sdk` (`getCapabilityMatrix()` before guessing)
3. **8DNA REST leaf** — `PATCH /api/projects/{id}/data` (same as `projects.patch_data`)
4. **Command bus** — `POST /api/commands/execute`
5. **New REST** — only when no MCP action, DNA path, or command fits

Full domain map: [`CONTEXT_FOR_AI.md`](CONTEXT_FOR_AI.md) · Action names: `GET /mcp/actions`

---

## UI journeys (human parity)

| Doc | Use when |
|-----|----------|
| [MCP_QUICKSTART.md](../MCP_QUICKSTART.md) | First `agentstack.execute` call |
| [USER_FEATURES_GUIDE.md](../USER_FEATURES_GUIDE.md) | Dashboard modules in plain language |
| [SANDBOX_AND_ENVIRONMENTS.md](../SANDBOX_AND_ENVIRONMENTS.md) | Generation fork, diff, promote |

---

## Canonical code examples

### Bootstrap (MCP)

```json
{
  "steps": [
    { "id": "p1", "action": "projects.get_projects", "params": {} },
    { "id": "p2", "action": "projects.get_stats", "params": { "project_id": { "from": "context.project_id" } } }
  ],
  "context": { "project_id": 2048 },
  "options": { "stopOnError": true }
}
```

### Leaf write (never full-blob `data=`)

```json
{
  "action": "projects.patch_data",
  "params": {
    "project_id": 2048,
    "path": "ecosystem.my_app.settings",
    "value": { "theme": "dark" },
    "write_mode": "merge"
  }
}
```

### Static site (SDK)

```typescript
import { createAgentStackSDK } from '@agentstack/sdk';

const sdk = createAgentStackSDK({ apiKey, projectId });
const { url } = await sdk.hosting.quickStart({
  html: '<!DOCTYPE html><html><body>Hello</body></html>',
  publish: true,
  bucketName: 'landing',
});
```

### Automation (dry-run first)

```json
{
  "steps": [
    { "id": "dry", "action": "logic.dry_run", "params": { "trigger": { "type": "signal", "name": "user_created" }, "actions": [], "seed": "demo" } },
    { "id": "on", "action": "logic.create", "params": { "name": "grant_trial", "enabled": false } }
  ],
  "context": { "project_id": 2048 }
}
```

---

## Related

- Long-running agents: `agentstack-architect`, `agentstack-migrator`, `agentstack-tenant-builder`
- Consumer kit: `genetic-ai-starter` profile `agentstack-app` · recipes `examples/agentstack/00–11`
- Tenant 8DNA mutations: prompt `agentstack_tenant_8dna_supply` · generation `fork` → `diff_vs_prod` → `promote`
