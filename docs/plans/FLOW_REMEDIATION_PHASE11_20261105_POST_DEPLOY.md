---
name: Flow Remediation Phase 11 (Post-deploy closure + remaining perf)
overview: "Close VPS verification loop, document shipped WG/MCP/hosting fixes, queue WG-PERF-03 and MCP alias ergonomics without version bump."
philosophy: philosophy/PHILOSOPHY_INDEX.md §0.4.26 AgentBus · §0.4.23 work graph loop · Creation over Conflict
genes:
  - core.hosting.bucket.gen1
  - core.agents.work_graph.gen1
  - core.mcp.self_description.gen1
  - core.fabric.access_guard.gen1
  - repo.engineering.founder_direct_ship.gen1
  - repo.ops.vps_deploy.gen1
status: active
date: 2026-11-05
---

# Phase 11 — Post-deploy closure

Philosophy filter (**Elegant Minimalism**, **Observability first**): one enqueue path for dev/prod builds; one membership role read SoT; one fleet eligibility param instead of `detail=full` lies.

## Shipped this session (substrate)

| ID | What | Where | Why (gene) |
|----|------|-------|------------|
| P11-DONE-DEPLOY | `-RebuildBackend` deploy success | VPS | `repo.ops.vps_deploy.gen1` |
| P11-DONE-DEV | Force dev rebuild + prefixed assets | MCP `hosting.site.build` · `site-vite-build.mjs` | `core.hosting.bucket.gen1` |
| P11-DONE-WG01 | `fleet_skipped` → `include_execution_eligibility` | `plan_get_fleet_gate.py` · `work_next.py` (2 sites) | `core.agents.work_graph.gen1` — avoids heavy `detail=full` second hop |
| P11-DONE-MCP-MEM | `shared/projects/membership_role.py` + `context=` threading | `project_membership_gate.py` · `gate_caps_membership.py` · `current_user_context.py` | `core.fabric.access_guard.gen1` — DRY + batch cache |
| P11-DONE-BUS | `@All` deploy receipt | `bots.bus.deliver` idempotency `deploy-flow-rem-20261105-v2` | `core.bots.fleet.gen1` |
| P11-DONE-IDX | Regenerated `mcp_agent_instruction_index.json` | `gen_mcp_agent_instruction_index.py` | `core.mcp.self_description.gen1` |

## TODO (detailed decomposition)

### P11-WG03 — Plan revision warm cache on compact `work_next`

**Symptom:** Repeated compact polls re-read full plan graph when `if_match_revision` unchanged.

**Steps:**

1. Open `shared/work_graph/work_next.py` — locate plan prefetch / `build_orchestrator_plan_read` call site (search `if_match_revision`).
2. Add module-level or request-scoped cache keyed by `(project_id, env_uuid, revision)` with TTL ≤ 5s (KISS — same process only).
3. On cache hit, skip DNA leaf read; set diagnostic `plan_prefetch_cache_hit: true`.
4. Test: `shared/tests/test_work_next_plan_cache.py` — two compact calls same revision → one read mock.
5. Philosophy: **Observability first** — expose hit/miss in `packet.diagnostics`, not silent skip.

**Example sketch:**

```python
# shared/work_graph/plan_revision_cache.py
def get_cached_plan_revision(key: PlanCacheKey) -> Optional[int]: ...
def note_plan_revision(key: PlanCacheKey, revision: int) -> None: ...
```

Wire in `compose_work_next` before `build_orchestrator_plan_read`.

---

### P11-MCP-04 — `hosting.site.build.status` self-desc `job_id` from enqueue

**Symptom:** Operators pass `bucket_name`/`target` to status (validation_error) — instruction_slice already says `job_id` but alias docs weak.

**Steps:**

1. `shared/mcp/hosting_instruction_atoms.py` — add `when_blocked.validation_error` next_action pairing enqueue → status with copied `job_id`.
2. `action_aliases.py` — ensure `hosting.site.build.status` example_request includes only `job_id`.
3. Run `python agentstack-core/scripts/gen_mcp_action_aliases_ts.py` + `npm run audit:closed-loop-harness:fast`.
4. Plugin skills `agentstack-hosting/SKILL.md` — one line: “always poll status with job_id from enqueue response.”

---

### P11-MCP-05 — Optional `build_status` alias accepting `bucket_name` + `target` (read latest job)

**Conflict note:** Creation over Conflict — prefer **one** poll shape (`job_id`). Only add resolver if bus/TG ops repeatedly fail validation.

**If approved:**

1. `hosting_site_build_queue.py` — `resolve_latest_job(project_id, bucket_name, target) -> job_id | None`.
2. Extend MCP schema optional `job_id` XOR `(project_id, bucket_name, target)`.
3. Tests in `test_hosting_build_status_mcp.py`.

---

### P11-UI-01 — CopilotBlockedWorkPanel copy sync

**File:** `agentstack-frontend/src/modules/projects/components/CopilotBlockedWorkPanel.tsx`

Replace any remaining “detail=full” fleet hint with “include_execution_eligibility” (matches WG-PERF-01).

---

### P11-CI-01 — Descriptor hash drift gate

After `work_graph_instruction_atoms` edits:

```bash
python agentstack-core/scripts/check_mcp_plan_actions_descriptor.py
npm run audit:agents-when-blocked-parity
```

Update fixture hashes only when intentional (document in Phase 10 doc WG-PERF-02 row).

---

### P11-OPS-01 — Post-deploy hosting checklist (runbook §)

Add to `docs/operations/PLATFORM_SHELL_HOSTING_RUNBOOK.md`:

1. `hosting.site.build` `target=dev` `force=true` `async=true`
2. `hosting.site.build.status` `{ "job_id": "<from enqueue>" }`
3. `curl` `/s/1/frontend--dev/build-id.txt` + one `/assets/index-*.js` under prefix
4. Log job_id in `@All` bus receipt

---

### P11-BUS-01 — Ack pending TG threads

Use `docs/operations/BOT_OWNER_AGENT_MESSAGE_BUS.md` ack playbook:

- Reply `[TG-xxxx]` on items addressed to `cursor-ops` only
- `bots.bus.ack` after deliver for hosting/nginx/eslint threads from intake snapshot

Command:

```bash
npm run bus:intake-snapshot
# read docs/_generated/bus_intake_latest.json → ack processed ids
```

---

## Dependency graph

```mermaid
flowchart LR
  deploy[VPS deploy] --> devBuild[dev force build]
  devBuild --> curlSmoke[curl prefixed assets]
  curlSmoke --> busAll[@All deliver]
  wg01[WG-PERF-01 shipped] --> uiCopy[P11-UI-01]
  wg03[P11-WG03 cache] --> ci[P11-CI-01]
  mcp04[P11-MCP-04 docs] --> mcp05[P11-MCP-05 optional]
```

---

## Anti-patterns (philosophy)

| Wrong | Right |
|-------|--------|
| Second `work_next` with `detail=full` for fleet | `include_execution_eligibility=true` once |
| Poll build status with bucket_name only | `job_id` from enqueue |
| Duplicate membership SQL in gates | `membership_role(..., context=)` |
| Tenant DNA in deploy.full | MCP generation supply |

---

## Verification commands

```bash
python -m pytest shared/tests/test_work_next_next_action_invariant.py shared/tests/test_bitmap_session_reconcile_hydrate.py -q
npm run audit:platform-shell-site-build
curl -sS https://agentstack.tech/s/1/frontend--dev/build-id.txt
```
