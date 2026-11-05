---
name: Flow Remediation Phase 12 (WG fleet gate + hosting poll self-desc + deploy)
overview: "Ship neighborhood fleet skip, resolution_cache on claim agent, hosting job_id poll ladder; VPS deploy + AgentBus @All."
philosophy: philosophy/PHILOSOPHY_INDEX.md §0.4.23 · §0.4.26
genes:
  - core.agents.work_graph.gen1
  - core.hosting.bucket.gen1
  - core.mcp.self_description.gen1
  - repo.engineering.founder_direct_ship.gen1
date: 2026-11-05
---

# Phase 12 — shipped in code (this session)

| ID | Task | Files | Principle |
|----|------|-------|-----------|
| P12-WG-01 | `expand=neighborhood` no longer loads fleet | `plan_get_fleet_gate.py` | KISS — RBAC compose ≠ fleet snapshot |
| P12-WG-02 | `resolve_claim_agent_id` uses `resolution_cache` | `work_next.py` | DRY — one resolver pass per node |
| P12-MCP-04 | Build status `validation_error` + anti-patterns + enqueue `next_actions` | `hosting_instruction_atoms.py`, `tools_hosting.py`, `action_aliases.py` | Self-desc |
| P12-TEST | `test_neighborhood_expand_skips_fleet` | `test_work_next_fleet_gate.py` | Dev Test Atlas |

## TODO backlog (Phase 12b)

### P12-WG-03 — `if_match_revision` on `agents.work_next` graph load

**Why:** Clients echo revision from packet; server ignores CAS → duplicate JSONB reads every poll.

**Where:** `agentstack-core/mcp/tools_agents.py` `_load_graph_for_home` · `shared/work_graph/mcp_graph_prefetch.py` `resolve_work_graph_prefetch(..., if_match_revision=)`.

**Steps:**

1. Thread `params.get("if_match_revision")` into loader (mirror `plan_claim` ~6233).
2. On match + stash hit, skip `_loader()`.
3. Tests: `test_plan_claim_prefetch_batch.py` symmetric cases for `agents.work_next`.

### P12-WG-04 — Compact `plan_get` metrics walk

**Where:** `orchestrator_read_facade.py` `plan_read_metrics_bundle` when slice lacks `bytes_estimate`.

**Fix:** Slice-local estimate or skip full-graph `plan_graph_metrics` on compact wire.

### P12-MCP-05 — Optional latest-job resolver on build_status (only if ops demand)

**Default:** Do not implement — keep `job_id`-only poll (Creation over Conflict).

---

## Deploy + bus checklist

1. `python agentstack-core/scripts/gen_mcp_action_aliases_ts.py` if aliases changed.
2. `deploy/vps/deploy-from-windows.ps1 -RebuildBackend`
3. `hosting.site.build` pid=1 bucket=frontend target=dev force=true async=true
4. Poll `hosting.site.build_status` with `job_id`
5. `bots.bus.deliver` to_handle `@All` idempotency key unique per deploy
