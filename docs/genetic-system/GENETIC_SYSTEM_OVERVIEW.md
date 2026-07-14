# Genetic System — overview

**Genetic tag:** `docs.public.genetic_system.overview.gen1`

The Genetic System answers one question for large, agent-heavy codebases: **where is the canonical place to edit?** Folders answer *where files live*; genetic tags answer *which meaning you are changing*.

---

## Problem without an address layer

| Failure mode | Symptom |
|--------------|---------|
| Unscoped discovery | 85k-token blind context per turn; ~28% useful tokens |
| Wrong-tree edits | Legacy file patched; canonical path untouched |
| Parallel contours | Second auth, second HTTP client, duplicate playbook |
| Context rot | Relevant file buried mid-prompt (accuracy drops before window limit) |
| Compounding errors | Long agentic runs multiply early navigation mistakes |

On **500+** files, blind grep every session is the default failure. On **5,000+**, it becomes a calendar and quality crisis — see genetic-ai-starter [KILLER_FEATURE_LARGE_PROJECTS](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/KILLER_FEATURE_LARGE_PROJECTS.md).

---

## Five-step workflow

1. **Intent** — frame the task (e.g. Creation over Conflict: one canonical path).
2. **Genetic tag** — `core.crm.hub.gen1`, `frontend.spa.dual_shell.gen1`, etc.
3. **AI_NAVIGATION_MAP** — tag → directory → when to open (Tier 0 / Tier 1).
4. **AI_INDEX.md** — 1–2 hot files + sideways links (MCP, cache, signals).
5. **Scoped edit** — patch canon; update index in the same PR if boundaries moved.

Reading order is fixed in `foundation.genetic_coding.gen1` and `docs/AI_INDEXING_SYSTEM.md` (monorepo).

---

## Six foundation pillars (compressed)

| Pillar | One line |
|--------|----------|
| Creation over Conflict | One canonical path — no permanent old+new dual stacks |
| Elegant Minimalism | Umbrella docs over ten heritage stubs |
| Absolute Optimization | Optimize **finding** the right edit, not token volume alone |
| Decomposition → Reassembly | tissue → organelle → atom; change one layer |
| Time-Decomposition-Completion | Steps with explicit “done” artifacts |
| Genetic coding + AI↔gene interface | Tags as stable addresses for humans and agents |

Full genes ship in the kit payload and as gene documents in the AgentStack monorepo (see [genetic-ai-starter](https://github.com/agentstacktech/genetic-ai-starter)).

---

## Beyond code

The same invariant applies wherever knowledge lives in artifacts:

| Layer | In code | Outside code |
|-------|---------|----------------|
| Address | `core.crm.hub.gen1` | `ops.incident.sev1.gen1` |
| Map | `AI_NAVIGATION_MAP` | Domain map → wiki / Notion section |
| Index | hot files | “read these 2 SoT docs first” |
| Wrong-tree | legacy module | stale PDF / draft policy |

Ops runbooks, support KB, legal policies, design systems, and research packs benefit when repeat tasks and **several candidate artifacts** exist.

---

## AgentStack platform inventory (reference)

Figures from platform snapshots and navigation map (Jul 2026) — regenerate via genetic-ai-starter `export-platform-stats.mjs`:

- **186** `AI_INDEX.md` repo-wide (**162** on platform packages)
- **406** `gen1` genes in philosophy
- **421** Tier-1 tags in the central map
- **~16** cross-cluster synergy rows (genetic-system-site narrative)
- **12.36×** measured compression on philosophy access path (AgentStack gene-access bench)

Consumer projects start from **genetic-ai-starter** (~20 payload genes, template map, Cursor rules) and grow Tier 1 rows as subsystems appear.

---

## Gene navigation vs neural runtime

Do not conflate:

| Contour | What it is |
|---------|------------|
| **Gene navigation** | Map, tags, indexes — how agents **find** where to edit |
| **Neural runtime** | Product latency, cache, managed organism — how the **live app** routes signals |

Economics in [AI_MODEL_ECONOMICS.md](AI_MODEL_ECONOMICS.md) are mostly **labor calendar** and discovery tax — not “neural magic.”

---

## Getting started

### On AgentStack (hosted product)

1. Use dashboard + MCP/SDK as today — [BUILD_YOUR_PRODUCT.md](../BUILD_YOUR_PRODUCT.md).
2. Install [cursor-plugin](https://github.com/agentstacktech/cursor-plugin) for map-aware rules and MCP.
3. Read platform genes via public GitHub philosophy index when extending the monorepo.

### In your own repository

```bash
npx @agentstack/genetic-ai-starter init --yes --profile standard \
  --target ./my-app --project-name "My App" --domain app
```

Then fill `docs/ai/AI_NAVIGATION_MAP.md`, add `AI_INDEX.md` per subsystem, run `doctor`.

For AgentStack consumers use profile **`agentstack-app`** — recipes, capability snapshot, SDK bootstrap: [genetic-ai-starter AGENTSTACK_APP_GUIDE](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/AGENTSTACK_APP_GUIDE.md).

---

## Agents in 2026 — why addressing is not optional

Three independent lines (summarized from research canvases and the genetic-system-site):

1. **Context rot** (Chroma, 2026) — accuracy falls as context grows, well before the claimed window; stuffing the whole repo fails.
2. **Prompt caching** (Anthropic / OpenAI / Google, 2026) — ~75–90% discount on **identical** prefixes; unstable grep-heavy prefixes miss cache.
3. **Compounding reliability** — per-step success `r` → chain success `r^n`; cutting wrong-tree rate helps **every** step.
4. **METR time horizons** (TH1.1, Jul 2026) — autonomous task horizon doubling ~every **89 days** since 2024; longer runs amplify early nav errors.

**Genetic tag + AI_INDEX** = short, stable, precise address instead of a large noisy context.

---

## See also

- [README.md](README.md) — hub index
- [AI_MODEL_ECONOMICS.md](AI_MODEL_ECONOMICS.md) — numbers, models, caveats
- [genetic-ai-starter DOC_HUB](https://github.com/agentstacktech/genetic-ai-starter/blob/main/meta/docs/DOC_HUB.md)
