---
name: Flow Remediation Phase 10 (Perf + Self-Desc)
overview: "Unit economics on work_next/MCP batch, relay log parity, remaining hosting VPS ship — after bus/MCP log + Phase 9 substrate."
philosophy: philosophy/PHILOSOPHY_INDEX.md §0.4.23 work graph loop · §0.4.26 AgentBus
genes:
  - core.agents.work_graph.gen1
  - core.mcp.self_description.gen1
  - repo.platform.unit_economics.gen1
  - core.fabric.access_guard.gen1
  - repo.engineering.founder_direct_ship.gen1
todos:
  - id: P10-DONE-WG02
    content: "WG-PERF-02 empty recommended_expand.default + detail_full_default + compact_hash ade6f864ba4f9f0c"
    status: completed
  - id: P10-DONE-MCP02
    content: "P10-FLOW-MCP-02 control-plane batch skips membership enrich when effective_project_role set"
    status: completed
  - id: P10-DONE-RELAY01
    content: "P10-FLOW-RELAY-01 relay no-room INFO + count_relay_no_room excludes suppressed DEBUG"
    status: completed
  - id: P10-SHIP-VPS
    content: "Deploy worker+API; force dev rebuild; curl /s/1/frontend--dev/assets prefixed"
    status: completed
  - id: P10-WG01
    content: "WG-PERF-01 fleet single-hop — include_execution_eligibility instead of detail=full"
    status: completed
  - id: P10-WG03
    content: "WG-PERF-03 plan graph if_match_revision cache on work_next prefetch hit"
    status: pending
  - id: P10-MCP01
    content: "P10-FLOW-MCP-01 work_graph_membership_error_code → membership_role + context"
    status: pending
  - id: P10-MCP03
    content: "P10-FLOW-MCP-03 ensure_mcp_l1_caps reuse _membership_role_cache before resolve_effective_project_role"
    status: pending
  - id: P10-MCP04
    content: "P10-FLOW-MCP-04 ensure_mcp_l1_caps once in _prepare_execute_batch_ingress; batch preflight spine flag"
    status: pending
  - id: P10-MCP05
    content: "P10-FLOW-MCP-05 sync plugin agentstack-work-graph SKILLS with loop_compact empty default"
    status: pending
  - id: P10-CI-VITEST
    content: "Vitest resolveBase() for site-vite-build.mjs HOSTING_* matrix"
    status: pending
  - id: P10-DOC-REGEN
    content: "gen_mcp_agent_instruction_index after hosting/work_graph atom edits"
    status: pending
isProject: false
---

# Flow remediation Phase 10 — perf, self-desc, ship (2026-11-05)

**Parents:** [Phase 9 deep decomposition](./FLOW_REMEDIATION_PHASE9_20261105_DEEP_DECOMPOSITION.md) · bus/MCP log master plan (`.cursor/plans/` — do not edit)

**Philosophy filter ([PHILOSOPHY_INDEX.md](../philosophy/PHILOSOPHY_INDEX.md)):**

| Pillar | Phase 10 |
|--------|----------|
| Creation over Conflict | One loop wire SoT (`work_next_loop_wire_compact`); one membership hydrate gate (`bitmap_session_reconcile`) |
| Elegant Minimalism | Throttle relay log + analyzer regex — no new metrics service |
| Observability first | `compact_hash` ratchet; `build_already_queued.requested_at`; analyze ACTION hints |
| 0.4.23 work graph | Cheap default poll; heavy panels only on `detail=full`, claim, or explicit expand |

---

## A. Shipped in repo (this wave)

### A.1 WG-PERF-02 — Self-desc loop matches unit economics

**Problem:** `discovery.describe` / skills told agents to pass `expand=['instruction_packet']` every poll → KB compile on “cheap” `agents.work_next`.

**Where:** `shared/work_graph/work_next.py` → `work_next_loop_wire_compact()`.

**Change:**

```python
"recommended_expand": {
    "default": [],
    "detail_full_default": ["instruction_packet"],
    "when_state_blocked": ["instruction_packet", "blocked_work"],
    "when_promote_pending": ["generation_pending"],
},
```

**Why (DRY/KISS):** Server already skipped compile when `expand` omitted (`work_next_needs_heavy_packet_build`); mismatch was **documentation + client habit**, not a second code path.

**Ratchet:** `shared/fixtures/mcp_plan_action_descriptors_v1.json` → `agents.work_next.compact_hash` = **`ade6f864ba4f9f0c`**.

**Tests:** `shared/tests/test_work_next_compact_compile_gate.py::test_loop_wire_recommended_expand_default_empty`.

**Atoms:** `shared/mcp/work_graph_instruction_atoms.py` — hot poll prose updated.

---

### A.2 P10-FLOW-MCP-02 — Skip DNA enrich on control-plane-only batches

**Problem:** JWT `member` + bitmap still forced `enrich_current_user_from_api_key` every `agents.work_next` execute.

**Where:** `shared/fabric/bitmap_session_reconcile.py` · `agentstack-core/mcp/current_user_context.py`.

**Change:**

```python
if role in ("", "user", "member"):
    acts = _batch_step_actions(steps)
    eff = str(ctx.get("effective_project_role") or ...).strip()
    if eff and acts <= WORK_GRAPH_CONTROL_PLANE_ACTIONS:
        return False  # bitmap reconcile only
```

Pass `context=` from `hydrate_mcp_execute_principal`; set `context["_mcp_execute_principal_hydrated"] = True` after enrich.

**Tests:** `shared/tests/test_bitmap_session_reconcile_hydrate.py`.

**Gene:** `core.fabric.access_guard.gen1`.

---

### A.3 P10-FLOW-RELAY-01 — Relay log + analyzer parity

**Where:** `social_chat_relay_bridge.py` (first cross-worker miss → **INFO** not WARN) · `log_policy_patterns.count_relay_no_room` (count only primary message, not DEBUG “suppressed repeat”).

**Why:** Multi-worker PG bus is expected; WARN storm polluted ops triage (gene heat should own rate, not duplicate WARN).

---

## B. Pending — high ROI (implement next)

### B.1 WG-PERF-01 — Fleet single-hop (P10-WG01)

**Symptom:** Compact `work_next` → `fleet_skipped` → synthesized `next_action` with `detail=full` → **second** MCP round trip.

**Files:**

- `shared/work_graph/plan_get_fleet_gate.py` — load fleet when compact rollup implies executable leaf
- `shared/work_graph/work_next.py` — `_synthesize_compact_next_action` (~582–588): do not promote `detail=full` when fleet could load in same request

**Test:** extend `shared/tests/test_work_next_next_action_invariant.py`.

**Philosophy:** 0.4.23 — one advance via observed workflow; no fake “cheap” packet that forces expensive follow-up.

---

### B.2 WG-PERF-03 — Plan graph revision cache (P10-WG03)

**Where:** `project_orchestrator_service.load_plan_graph` · `tools_agents._load_graph_for_home` · existing prefetch at `_load_plan_graph_for_get`.

**Pattern (ECS):** Graph entity + revision component; skip full leaf read when `if_match_revision` matches batch stash.

**DNA rule:** leaf read only — `repo.engineering.dna_protein_data_plane.gen1`.

---

### B.3 P10-FLOW-MCP-01/03/04 — Membership DRY

| ID | File | Action |
|----|------|--------|
| MCP-01 | `project_membership_gate.py` | `work_graph_membership_error_code` → `membership_role(context=)` |
| MCP-03 | `gate_caps_membership.py` | Try `_membership_role_cache` before `resolve_effective_project_role` |
| MCP-04 | `controller.py` | `ensure_mcp_l1_caps` once in `_prepare_execute_batch_ingress`; optional `_batch_needs_mutation_preflight` |

---

### B.4 P10-FLOW-MCP-05 — Plugin skills sync

Update `provided_plugins/*/skills/agentstack-work-graph/SKILL.md`:

- Hot loop: `detail=compact`, **no default expand**
- Blocked: add `expand=['instruction_packet','blocked_work']`
- Full detail: `detail=full` or `detail_full_default`

---

### B.5 P10-SHIP-VPS — Hosting preview (still ops)

```bash
# After deploy with site-vite-build + apply_site_build_target_env
curl -s "https://agentstack.tech/s/1/frontend--dev/" | findstr assets
npm run audit:platform-shell-site-build
python -m pytest agentstack-core/tests/unit/test_site_build_service.py -q
```

---

## C. CI / verification matrix

| Gate | Command |
|------|---------|
| Loop wire | `python -m pytest shared/tests/test_work_next_compact_compile_gate.py -q` |
| Hydrate | `python -m pytest shared/tests/test_bitmap_session_reconcile_hydrate.py -q` |
| Descriptor | `python agentstack-core/scripts/check_mcp_plan_actions_descriptor.py` |
| Hosting | `npm run audit:platform-shell-site-build` |
| Work graph gene | `npm run test:scope -- --gene core.agents.work_graph.gen1` |
| MCP remediation | `npm run test:scope -- --gene core.mcp.remediation.gen1` |

---

## D. Gap register (G-21–G-24)

| ID | Gap | Status |
|----|-----|--------|
| G-21 | Describe default expand ≠ execute cost | **Fixed** WG-PERF-02 |
| G-22 | Member JWT re-enrich every work_next | **Fixed** P10-MCP-02 |
| G-23 | Relay WARN counts as incident | **Fixed** P10-RELAY-01 |
| G-24 | fleet_skipped two-hop | **Open** P10-WG01 |

---

## E. Anti-patterns

| Wrong | Right |
|-------|-------|
| Hardcode `expand=instruction_packet` on every timer poll | Compact default; expand when blocked or `detail=full` |
| New “fast work_next” MCP action | Fix `loop_compact` + gates |
| Second membership `find_one` per batch step | Request-scope cache + control-plane skip |
| nginx sub_filter for Vite base | Worker env + `site-vite-build.mjs` |

---

*Phase 10 — execute pending TODOs in order B.1 → B.3 → B.4 → B.5; no version bump without founder order.*
