# Platform Supply Free.ai — Flow Audit Decomposition

**Gene:** `core.llm.partner_providers.gen1` · `core.economy.llm_energy.gen1` · `core.mcp.self_description.gen1`  
**Philosophy:** Creation over Conflict · Elegant Minimalism (net LOC↓) · Observability first  
**Patterns:** SOLID (one debit choke) · DRY (open provider map) · ECS (provider components) · KISS (prefer policy, no LiteLLM)

## Product SoT

Free.ai = **platform COGS**. Users spend `builder_energy` (subscriptions + free pool). `platform_supply=true`, `debit_energy=true`. Prefer via `builder_config.llm_free_calls`.

## Gap register (this audit)

| ID | Sev | Problem | Fix |
|----|-----|---------|-----|
| F1 | P0 | Fleet double energy debit (per-call + end-of-run) | Single choke: skip end consume when LLM already debited |
| P16 | P0 | Admin save collapses `ai_providers` to triple → wipe freeai | Open-map merge |
| F9 | P1 | Policy reloads DNA every call; duplicate list_ids+catalog | Cache policy with specs; list once |
| F5 | P1 | Project connect skips key cache clear | Reuse clear_all_scopes |
| F3 | P1 | `energy_low` never gets `energy_current` | Peek balance only when mode=energy_low |
| F6/P17 | P1 | MCP/runbook stale “no energy” copy | Self-desc atoms aligned to supply |
| F7 | P1 | No `platform_supply` on usage/OpTrace | Attr on ledger |
| P18 | P1 | No ai.providers instruction enricher | Mirror energy_instruction_atoms |
| P20 | P1 | Zod strips platform_supply | Extend schema |
| P19 | P1 | FE save no catalog invalidation | Invalidate llm-providers |
| P21 | P2 | `selectedDebitEnergy` unused | Wire AgentSpecEnergyForm + purchase opts |
| P22 | P1 | `provider_call_with_debit` omits provider_id | Infer from provider object |
| P24 | P2 | ai.providers self-desc missing next_actions | Mirror energy atoms |
| F13 | P2 | Duplicate catalog/list | Derive providers from items |
| F12 | P2 | platform_supply duplicated into extra | Exclude in spec_from_dict |
| P23 | P2 | Treasury strip full `/data` | Prefer leaf / lighter read |

## Execution order

1. P16 open map · F1 single debit · F5 cache clear ✅  
2. F9/F13 registry cache + list once · F7 attrs · F12 ✅  
3. P17/P18 self-desc · runbook/docs ✅  
4. P20/P19/P21 FE · F3 energy_low when cheap ✅  
5. P22 provider_id on debit choke · P24 next_actions ✅  

## Detailed TODO (closed this pass)

| Step | Where | Why (philosophy) |
|------|-------|------------------|
| F3 peek | `provider_key.py` + `tools_ai_providers.py` | `allows_auto_free(energy_current=)` only when `mode=energy_low` — KISS, avoid DNA/energy I/O on `always` |
| P21 CTA | `AgentSpecEnergyForm` + `canPurchaseEnergyPack` opts | Hide buy-energy for `debit_energy=false`; keep for Free.ai platform_supply |
| P22 debit | `debit.py` `_infer_provider_id` | SOLID single choke — bypass sites pass provider for partner exempt |
| P24 self-desc | `ai_providers_instruction_atoms.py` | `next_actions` ladder for agents (Observability first) |
| Stale copy | `llm_free_calls.py` module doc + nav map | Free.ai **does** debit energy |
| Fallback catalog | `agents_endpoints.list_agent_llm_providers` | Fallback items include `platform_supply` |

## Remaining (ops — not code)

1. Core **deploy** so live MCP exposes `ai.providers.*`  
2. Founder `sk-free-…` → `ai.providers.connect` + `probe` + `agents.run` smoke  

## Industry map (no new services)

LiteLLM default routing → `llm_free_calls.prefer` + `reorder_providers_prefer_free`. OpenRouter prepaid → `debit_energy=false` external only.
