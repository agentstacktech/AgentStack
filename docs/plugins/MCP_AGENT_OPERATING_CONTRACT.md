# MCP Agent Operating Contract

**Genetic tag:** `core.mcp.instruction_plane.gen1`  
**Machine index:** `docs/_generated/mcp_agent_instruction_index.json` (do not hand-maintain action tables)

## Execute envelope

- Single tool: `agentstack.execute` at `https://agentstack.tech/mcp`
- Batch shape: `{ "steps": [...], "context": { "project_id": N, "flow_id": "optional" }, "options": { "stopOnError": true } }`
- Mandatory order: authenticate → pick project → bind `context.project_id` → `agents.work_next` (recipe `mcp_work_loop_v1`); call `packet.next_action` when set

## Effect kinds

| kind | Meaning | Examples |
|------|---------|----------|
| `read` | No mutation | `projects.get_project`, `discovery.list` |
| `evaluate` | Check without write | `rbac.check_permission`, `generation.gates`, `logic.dry_run` |
| `mutate` | Durable change | `projects.patch_data`, `bots.create` |
| `destructive` | Irreversible | `buffs.cancel_buff` |

`external_side_effect: true` marks channel/payment/publish actions (`bots.go_live`, `payments.create`, …).

## Discovery ladder

See generated index `onboarding.discovery_ladder`. Step 1 is `agents.work_next`. Schema lookup is `discovery.search` / `discovery.describe`. **`preflight.check`** before a tenant mutation.

**Workflow SoT:** prefer `data.routed_goal` / `selected_workflow` from `POST /mcp/discover/by_intent` over legacy `intents[0]` keyword ranking when both are present.

## Attention, behavior, and tokens

Work Graph changes the agent **before** it spends the window on domain work.

| | Catalog shopping | Work Graph |
|--|------------------|------------|
| After `context.project_id` | `discovery.search` / `discovery.list` | `agents.work_next` |
| Each step | Pick a row from the page | Call `packet.next_action` |
| When blocked | Invent another tool | `when_blocked` on that action |

**Measured locally on 2026-10-07** (registry build, not a production sample):

| Payload | UTF-8 bytes | Rough tokens (bytes ÷ 4) |
|---------|-------------|---------------------------|
| Example `next_action` + two alternatives (`agents.plan_claim`) | 250 | ~63 |
| `discovery.search` compact page, first 20 rows | 8,889 | ~2,200 |
| Handshake contract JSON (`build_ai_prompt_contract`, en-US), once | 11,297 / 16,384 cap | ~2,800 |
| Compact page, 50 rows (HTTP max) | 20,852 | ~5,200 |
| Full registry catalog JSON (795 actions) | 1,680,071 | ~420,000 |

The contract prose the same day was **7,258 / 8,192** characters. Bytes ÷ 4 is an English size estimate, not a provider tokenizer and not a bill.

**Operator targets** (not observed production rates): follow `next_action` on ≥ 90% of autonomous loops (WG-SLO-03); catalog shopping while a packet is present ≤ 5% (WG-SLO-04). See [WORK_GRAPH_SLO.md](../operations/WORK_GRAPH_SLO.md).

Principles: one executable step (Creation over Conflict), an 8 KiB handshake instead of a catalog dump (Elegant Minimalism), targets labeled separately from the byte snapshot (Observability first).

## Closed-loop autonomy (projects + business)

**Prompt:** `GET /mcp/prompts/get?name=agentstack_closed_loop_autonomy` (after session setup).

**Machine ladder:** `discovery_meta.onboarding.closed_loop_ladder` — same stages as production plan: GOAL → ROUTE → PLAN → SKILLS → EXECUTE → EVIDENCE → VERIFY → RECOVER.

| Surface | What agents do |
|---------|----------------|
| **Single project** | Plan graph (`agents.plan_*`), orchestrator (`agents.orchestrate`), fleet templates for specialists |
| **Business head** | `business.create_composite` / `business.command_snapshot`; run the loop per organ `context.project_id` |
| **Specialized teams** | Taxonomy `agents.team.create` → templates + plan parallel nodes — not integration catalog hijack |

**Verify honesty:** empty completion predicates → `not_evaluable` (not passed). Plan patch conflicts → fail with revision; do not auto-retry CAS.

**Platform self-check (operators):** `repo.ops.closed_loop.harness.gen1` · `npm run audit:closed-loop-harness`.

## List projection (Phase 6)

- `bots.list`, `logic.list`, `assets.list` default `projection=summary` (ECS `card_row` slice).
- Use `projection=full` only when mutating or editing entity specs.

## Batch envelope

- `options.envelope`: `compact` (default) omits duplicate `data` when identical to `capability_result.data`.
- Top-level `partial_success`, `succeeded_count`, `failed_step_ids` when `continueOnError` or mixed step outcomes.

## Permissions

- Prefer `permissions.effective` over ad-hoc `rbac.check_permission` loops before destructive work.
- `context.resolve` when `context.project_id` is missing; handle `ambiguous_project_context`.

## Errors

Execute errors normalize to `{ code, message, retryable, category, suggested_actions }`. See `mcp_recovery.py` hints.

## Safe tenant change

Prefer recipe `mcp_universal_safe_change` or domain recipes (`mcp_bot_ai_release`, `mcp_hosting_edit_site_safe`, …) with verify steps from `instruction_slice.verification`.

**Related:** [WORK_GRAPH.md](./WORK_GRAPH.md) · [CONTEXT_FOR_AI_MCP.md](./CONTEXT_FOR_AI_MCP.md) · [MCP_QUICKSTART.md](../MCP_QUICKSTART.md)
