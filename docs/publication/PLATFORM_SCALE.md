# AgentStack platform scale (public facts)

**As of:** 2026-05-28 · **Core line:** 0.4.13  
**Use for:** LinkedIn, marketplace listings, investor one-liners, plugin copy — **do not hand-edit counts**; refresh this file after major MCP waves.

**Russian edition:** maintained in the monorepo (not mirrored here).

---

## Headline numbers

| Metric | Value | Notes |
|--------|------:|-------|
| **MCP tools registered** | **311** | Server registry (`MCP_TOOLS_REGISTRY`) at startup |
| **MCP catalog actions** | **332** | `GET /mcp/actions` — includes commerce REST hints and catalog aliases |
| **Action domains** | **31** | Top-level prefix before `.` in action ids |
| **IDE entry tool** | **1** | `agentstack.execute` — batch steps, discovery via `/mcp/actions` |
| **Plugin surfaces** | **4** | Cursor, Claude Code, GPT, VS Code |

**Marketing shorthand (accurate):** *300+ MCP tools* · *330+ agent actions* · *one protocol for humans and AI*.

---

## Top domains (by action count, May 2026)

| Domain | Actions | Typical use |
|--------|--------:|-------------|
| `social` | 83 | Messenger, feeds, channels, presence |
| `agentnet` | 27 | AgentCoin ledger, bridge, checkpoints, BNB rail |
| `integrations` | 22 | Integration Hub, webhooks, recipes |
| `agents` | 21 | Agents Fleet CRUD, runs, policy |
| `logic` | 19 | Logic Engine rules and execution |
| `hosting` | 15 | Sites, publish pipeline, buckets |
| `projects` | 14 | Project lifecycle, users, stats |
| `scheduler` | 12 | Tasks, pools, cron-style jobs |
| `buffs` | 10 | Trials, subscriptions, limits |
| `rag` | 10 | Collections, ingest, hybrid search |
| `storage` | 5 | Project files, quotas, hub summary (pairs with hosting) |

**Hosting + files:** 15 `hosting.*` actions (quick-start, ZIP deploy, releases) + 5 `storage.*` — sites at `/s/{projectId}/{bucketName}/`, SPAs at `/a/...`. Public funnel: https://agentstack.tech/host-site

Full matrix: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

---

## How to verify (production or staging)

```http
GET https://agentstack.tech/mcp/health
GET https://agentstack.tech/mcp/actions
```

Expect `tools_count` / domain totals consistent with this sheet after deploy. Strict startup (`AGENTSTACK_STRICT_STARTUP=1`) fails if registered tools fall below `AGENTSTACK_MCP_TOOLS_MIN` (default **280**).

---

## Refresh checklist (maintainers)

1. Regenerate capability matrix from the platform release tooling (`gen_capability_matrix.py`).
2. Update counts in this file and the Russian companion (monorepo).
3. Sync [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).
4. Sweep publisher copy and showcase briefs (monorepo `docs/publication/`).

**Genetic tag:** `docs.publication.platform_scale.gen1`
