# Genetic System — public documentation hub

**Genetic tag:** `docs.public.genetic_system.gen1`  
**Audience:** Builders, integrators, and AI-agent teams using AgentStack or [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter).

The **Genetic System** is AgentStack's **Navigation OS**: stable semantic addresses (genetic tags), a central map, local indexes, and philosophy genes — so humans and agents land in the right files without repo-wide discovery every session.

---

## Start here

| Document | Purpose |
|----------|---------|
| [GENETIC_SYSTEM_OVERVIEW.md](GENETIC_SYSTEM_OVERVIEW.md) | What it is, how it works, when to use it |
| [AI_MODEL_ECONOMICS.md](AI_MODEL_ECONOMICS.md) | Labor vs tokens, models, benchmarks, caveats (from internal economics canvases) |
| [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter) | Portable kit for **your** repo (`npx @agentstack/genetic-ai-starter init`) |
| [BUILD_YOUR_PRODUCT.md](../BUILD_YOUR_PRODUCT.md) | AgentStack product + SDK + optional kit bootstrap |

**Interactive explainer (monorepo):** [docs/genetic-system-site/](https://github.com/agentstacktech/AgentStack/tree/master/docs/genetic-system-site) — one-page site with RU / EN / PT and dark theme. Serve locally: `npx serve docs/genetic-system-site -p 5179`.

---

## Core invariant

```
Intent → genetic tag → AI_NAVIGATION_MAP → AI_INDEX.md → 1–2 hot files → scoped edit
```

- **Genetic tag** — stable address (`domain.subsystem.role.gen1`), survives refactors.
- **Map** — one-screen registry: tag → path → when to read (Tier 0 package roots, Tier 1 subsystems).
- **Index** — local hot files + cross-links; updated in the same PR when boundaries change.
- **Genes** — philosophy and engineering rules in git (genetic tags + gene documents on the platform; payload copy in the kit).

This is **not** vector RAG by default — it is **address-first** navigation. RAG and MCP remain complementary surfaces on AgentStack.

---

## Platform vs kit

| Layer | Where | Role |
|-------|-------|------|
| **AgentStack monorepo** | Private / GitHub `AgentStack` | Full map (~267 Tier-1 tags), ~186 `AI_INDEX.md`, ~406 `gen1` genes, MCP, dual-shell |
| **genetic-ai-starter** | Public kit repo | Same Navigation OS pattern for **any** consumer repo; harness-proven rollout |
| **Cursor plugin** | [cursor-plugin](https://github.com/agentstacktech/cursor-plugin) | Rules, skills, MCP — works best when the repo has a map |
| **This mirror** | `agentstack_repo/` | Integrator-facing English docs only |

---

## Measured highlights (honest scope)

| Claim | Source | Scope |
|-------|--------|--------|
| **12.36×** philosophy context compression | AgentStack gene-access bench | Philosophy / gene access path, not arbitrary code |
| **186** live `AI_INDEX.md` | Platform inventory via genetic-ai-starter `platform-stats.snapshot.json` (Jul 2026) | AgentStack monorepo (`aiIndexFilesRepoTotal`) |
| **406** `gen1` genes | same snapshot `philosophyGenes` | Philosophy corpus |
| **421** Tier-1 tags | same `navigationMapTier1Tags` | Central navigation map |
| **~17** feature touches to break-even | EST model in economics canvases | Order of magnitude; depends on team rate |
| **P(save>0)=1.0** | AgentStack Monte Carlo release-cost simulation | Simulation with wide jitter — not prod A/B |
| **Harness weak 0% → kit+idx 100%** | genetic-ai-starter shop-api fixture | Synthetic transcripts; effect order transfers |

Details and caveats: [AI_MODEL_ECONOMICS.md](AI_MODEL_ECONOMICS.md).

---

## Related public docs

- [plugins/CONTEXT_FOR_AI.md](../plugins/CONTEXT_FOR_AI.md) — plugin + map-first workflow
- [plugins/AGENTSTACK_PLUGIN_PHILOSOPHY.md](../plugins/AGENTSTACK_PLUGIN_PHILOSOPHY.md) — DNA / genetic coding narrative
- [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) — `agentstack.execute` catalog
- [explanation/FABRIC_ONE_PLATFORM.md](../explanation/FABRIC_ONE_PLATFORM.md) — platform fabric

**Monorepo canonical narrative:** `docs/AI_INDEXING_SYSTEM.md` on GitHub `AgentStack` (not mirrored in full here).
