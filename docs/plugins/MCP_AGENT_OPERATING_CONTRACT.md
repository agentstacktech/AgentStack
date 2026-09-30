# MCP Agent Operating Contract

**Genetic tag:** `core.mcp.instruction_plane.gen1`  
**Machine index:** `docs/_generated/mcp_agent_instruction_index.json` (do not hand-maintain action tables)

## Execute envelope

- Single tool: `agentstack.execute` at `https://agentstack.tech/mcp`
- Batch shape: `{ "steps": [...], "context": { "project_id": N, "flow_id": "optional" }, "options": { "stopOnError": true } }`
- Mandatory order: authenticate → pick project → bind `context.project_id` → domain work

## Effect kinds

| kind | Meaning | Examples |
|------|---------|----------|
| `read` | No mutation | `projects.get_project`, `discovery.list` |
| `evaluate` | Check without write | `rbac.check_permission`, `generation.gates`, `logic.dry_run` |
| `mutate` | Durable change | `projects.patch_data`, `bots.create` |
| `destructive` | Irreversible | `buffs.cancel_buff` |

`external_side_effect: true` marks channel/payment/publish actions (`bots.go_live`, `payments.create`, …).

## Discovery ladder (10 steps)

See generated index `onboarding.discovery_ladder` — session setup → **`discovery.status`** → contract → summary → `discovery.search`/`discovery.describe` → intent → hot schemas → playbook → recipes → **`preflight.check`**.

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

**Related:** [CONTEXT_FOR_AI_MCP.md](./CONTEXT_FOR_AI_MCP.md) · [MCP_QUICKSTART.md](../MCP_QUICKSTART.md)
