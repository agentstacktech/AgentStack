# AgentStack platform scale (public facts)

**As of:** 2026-07-22 · **Core line:** <!-- stats:platform_version -->0.4.15<!-- /stats:platform_version -->  
**Use for:** LinkedIn, marketplace listings, investor one-liners, plugin copy — **do not hand-edit counts**; refresh via the platform stats codegen chain (`docs.freshness.living.gen1`).

**Russian edition:** [PLATFORM_SCALE_RU.md](PLATFORM_SCALE_RU.md)

**Maintainers (monorepo SoT):** `docs/publication/platform-stats.snapshot.json` on the default branch.

---

## Headline numbers

| Metric | Value | Notes |
|--------|------:|-------|
| **MCP catalog actions** | **<!-- stats:total_actions -->494<!-- /stats:total_actions -->** | `GET /mcp/actions` / `MCP_CAPABILITY_MATRIX.md` Total actions |
| **Action domains** | **<!-- stats:mcp_domains -->45<!-- /stats:mcp_domains -->** | Domain sections in capability matrix (codegen) |
| **MCP tools registered** | **311** | Server registry at startup (`MCP_TOOLS_REGISTERED`) — not the same as catalog actions |
| **IDE entry tool** | **1** | `agentstack.execute` — batch steps, discovery via `/mcp/actions` |
| **Plugin surfaces** | **4** | Cursor, Claude Code, GPT, VS Code |

**Marketing shorthand (accurate):** *450+ agent actions* · *40+ domains* · *one protocol for humans and AI*.

---

## Top domains (by action count, Jul 2026)

| Domain | Actions | Typical use |
|--------|--------:|-------------|
| `social` | 83 | Messenger, feeds, channels, presence |
| `integrations` | 48 | Integration Hub, webhooks, recipes |
| `agentnet` | 43 | AgentCoin ledger, bridge, checkpoints, rails |
| `commerce_rest` | 39 | Marketplace / commerce REST catalog hints |
| `agents` | 25 | Agents Fleet CRUD, runs, policy |
| `logic` | 19 | Logic Engine rules and execution |
| `commerce` | 18 | Commerce kit MCP actions |
| `hosting` | 18 | Sites, publish pipeline, buckets |
| `bots` | 17 | Bots Fleet |
| `crm` | 17 | CRM tissue |
| `projects` | 14 | Project lifecycle, users, stats |
| `scheduler` | 12 | Tasks, pools, cron-style jobs |

Full matrix: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) (public mirror; redacted).

---

## How to verify (production or staging)

```http
GET https://agentstack.tech/mcp/health
GET https://agentstack.tech/mcp/actions
```

Expect catalog totals consistent with this sheet after deploy.

---

## Refresh checklist (maintainers)

1. Regenerate the MCP capability matrix via the platform stats codegen chain (internal freshness runbook on the default branch).
2. `node scripts/codegen-platform-stats.mjs`
3. Align `shared/constants.py` `MCP_CATALOG_ACTIONS` / `MCP_ACTION_DOMAINS`
4. Update this file + `PLATFORM_SCALE_RU.md`
5. `node scripts/patch-public-doc-stats.mjs`
6. `npm run audit:stats-plane-parity`

**Genetic tag:** `docs.publication.platform_scale.gen1` · Living Plane: `docs.freshness.living.gen1`
