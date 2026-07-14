# Genetic System — AI model economics

**Genetic tag:** `docs.public.genetic_system.economics.gen1`

This document summarizes **labor vs token** economics for the Genetic System, synthesized from internal economics canvases (`genetic-system-ai-model-economics`, `agentstack-genes-release-economics`, `agentstack-platform-sdk-leverage`) and the public [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter) harness.

**Read this with caveats.** Numbers are **models and simulations** unless labeled as measured in CI. They inform prioritization — they are not guaranteed savings on your calendar.

---

## Two cost lines

| Line | What you pay | Genetic System lever |
|------|--------------|----------------------|
| **Labor** | Engineer time, calendar, rework | Fewer wrong-tree edits, faster onboarding, less grep archaeology |
| **Tokens** | API spend per agent turn | Shorter stable prefixes, less blind context, better cache hit rate |

**Primary ROI is labor**, not shaving tokens alone. Token savings are a **secondary** benefit when prefixes stabilize (map → index → hot file).

---

## Philosophy compression (measured)

| Metric | Value | Source |
|--------|-------|--------|
| Raw philosophy context | ~103,222 tokens | AgentStack gene-access bench (`bench_gene_access.json`) |
| Gene-indexed access path | ~8,350 tokens | same |
| **Compression ratio** | **12.36×** | same |

**Scope:** philosophy / gene access — not “compress your entire codebase.” The win is **addressed reads** instead of dumping all genes.

## Platform inventory (measured snapshot)

Regenerate via genetic-ai-starter `export-platform-stats.mjs` (Jul 2026 export):

| Metric | Value |
|--------|-------|
| Genes | **406** |
| `AI_INDEX.md` (repo / platform) | **186** / **162** |
| Tier-1 map tags | **421** |
| Kit payload genes | **27** |

Cross-cluster SYN **~16** is from the interactive genetic-system-site narrative, not the snapshot.

---

## Navigation harness (synthetic)

From genetic-ai-starter shop-api fixture transcripts (weak baseline vs kit + indexes):

| Scenario | Score (0–10) | Pass rate |
|----------|--------------|-----------|
| Weak (no map) | ~2.5 | 0% |
| Kit + indexes | ~9 | 100% |

**Interpretation:** effect **order** transfers to real repos; absolute scores depend on project shape. Run `npm run harness` in your clone after init.

### EST gene harness (platform internal)

| Metric | Before | After (indexed) |
|--------|--------|-----------------|
| Navigation failure rate | ~22% | ~5% |
| Token factor (discovery) | — | ~0.62 |
| Retry factor | — | ~0.85 |

These are engineering estimates used in release economics canvases — not production A/B.

---

## Break-even and Monte Carlo

| Model input | Typical value | Notes |
|-------------|---------------|-------|
| FTE-week cost | ~$3,500 | Release economics canvas default |
| Break-even touches | ~**17** | Feature-sized tasks touching the map |
| Monte Carlo `P(save>0)` | **1.0** | AgentStack Monte Carlo release-cost simulation with wide jitter |

**Caveat:** Monte Carlo with generous uncertainty still shows positive expected value because wrong-tree rework dominates at scale. Your team rate and task mix may differ.

---

## Release archetypes (weeks saved, order of magnitude)

From `agentstack-genes-release-economics` canvas — **FTE-week = $3,500**, turn-cost model:

| Archetype | Description | Indicative week savings |
|-----------|-------------|-------------------------|
| **A** | Greenfield app on AgentStack + kit | Highest — map + SDK compound |
| **B** | Brownfield add feature (500–2k files) | Medium-high — discovery tax cut |
| **C** | Large monorepo (5k+ files) | High — blind grep fails without map |
| **D** | SDK-only consumer | Medium — leverage table below |
| **E** | Docs / ops / KB only | Medium — same invariant, non-code artifacts |

Exact week ranges vary by team; see genetic-ai-starter [VALUE_AND_ROI_BY_PROJECT_SIZE](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/VALUE_AND_ROI_BY_PROJECT_SIZE.md) and [DOC_CLAIMS_AUDIT](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/DOC_CLAIMS_AUDIT.md).

---

## Platform SDK leverage (calendar)

From `agentstack-platform-sdk-leverage` canvas — **weeks not spent rebuilding**:

| Module | Typical save | Why |
|--------|--------------|-----|
| Auth + sessions | 2–4 w | Hosted identity, JWT, project scope |
| Payments / wallet | 2–5 w | agUSD, MCP commerce tools |
| 8DNA / project data | 1–3 w | Genetic project records, not ad-hoc JSON |
| MCP + `agentstack.execute` | 1–2 w | Tool catalog vs bespoke integrations |
| Dual-shell SPA | 3–6 w | Audience, nav, view-as, pages map |
| RAG / neural cache | 1–3 w | Platform substrate vs DIY vector stack |

**Genetic navigation** stacks on top: SDK removes build weeks; map removes **find-and-fix** weeks inside what you still own.

---

## Model and agent landscape (2026)

Factors that increase the value of stable addresses:

| Signal | Implication for navigation OS |
|--------|-------------------------------|
| **Context rot** | Long prompts hurt accuracy before window limits — prefer 2-file index reads |
| **Prompt caching** | Stable `map → index` prefix caches; grep roulette does not |
| **METR TH1.1** | Autonomous horizons doubling ~every 89 days — errors compound over longer runs |
| **Multi-agent fleets** | Shared tags prevent agent A and B patching different trees |

---

## What we do not claim

- Genetic tags do **not** replace tests, code review, or security review.
- Map maintenance has cost — threshold ~10+ integration points or non-obvious boundaries (see `docs/AI_INDEXING_SYSTEM.md`).
- **12.36×** is not “12× faster development” — it is philosophy access compression.
- Harness **100%** is a synthetic fixture — use as regression guard, not a sales guarantee.

---

## Audit trail

| Artifact | Repo |
|----------|------|
| `metrics.snapshot.json` | genetic-ai-starter |
| `platform-stats.snapshot.json` | genetic-ai-starter |
| `DOC_CLAIMS_AUDIT.md` | genetic-ai-starter |
| Gene-access bench (`bench_gene_access.json`) | AgentStack monorepo (internal measurement) |
| Monte Carlo release-cost JSON | AgentStack monorepo (internal measurement) |

---

## See also

- [GENETIC_SYSTEM_OVERVIEW.md](GENETIC_SYSTEM_OVERVIEW.md)
- [genetic-ai-starter GENETIC_SYSTEM_ECONOMICS](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/GENETIC_SYSTEM_ECONOMICS.md)
- [Interactive site (RU/EN/PT)](https://github.com/agentstacktech/AgentStack/tree/master/docs/genetic-system-site)
