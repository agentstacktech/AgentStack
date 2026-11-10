# LLM Energy — Gap Register

**Genetic tag:** `core.economy.llm_energy.gen1`  
**ADR:** [LLM_ENERGY_AND_BYOK.md](../adr/LLM_ENERGY_AND_BYOK.md)  
**Status:** Active — tracks closure of monetization gaps

| ID | Severity | Symptom | Resolution | Status |
|----|----------|---------|------------|--------|
| GAP-P0-01 | P0 | Card webhook did not credit `builder_energy` | `grant_energy_pack_from_payment` + `pack_tier` metadata | Closed |
| GAP-P0-02 | P0 | BYOK still debited energy | `should_debit_energy(key_source)` in `ai_calls.py` choke | Closed |
| GAP-P0-03 | P0 | Project runs used actor user pool | `EnergyOwner.project` + Fleet/Finance facades | Closed |
| GAP-P0-04 | P0 | Unpaid `purchase-pack` in prod | 402 `payment_required`; card via AgentPay | Closed |
| GAP-P1-01 | P1 | Admin vs Python energy scale drift | Unified defaults FREE=8, packs 50/200/500 | Closed |
| GAP-P1-02 | P1 | `default_key_source` ignored | `llm_payer` policy in KeySelector | Closed |
| GAP-P1-03 | P1 | Project bots skipped energy | Skip only `mentor_channel` / `bill_to=none` | Closed |
| GAP-P1-04 | P1 | Double debit in stages + message_flow | Single post-call hook in `call_ai_provider` | Closed |
| GAP-P1-05 | P1 | GET balance side-effects | `refresh` query param; mutate on reserve/consume only | Closed |
| GAP-P1-06 | P1 | `project_id` ignored on balance | `GET /energy/balance?project_id=` | Closed |
| GAP-P1-07 | P1 | Two ecosystem key writers | Builder APIKeys read-only → Environment secrets | Closed |
| GAP-P1-08 | P1 | Sandbox simulate wrote wrong path | `add_purchased_energy` in simulation | Closed |
| GAP-P1-11 | P1 | PriceBook double-charge with energy | `payer_kind=platform` skips `rate_and_record` | Closed |
| GAP-P2-01 | P2 | No SDK energy module | `sdk.energy` package | Closed |
| GAP-P2-03 | P2 | Two purchase UX paths | `EnergyPackCheckout` + TopUpBalanceForm | Closed |
| GAP-P1-09 | P1 | LLM bypass without metering | `debit_energy_after_llm_call` on orchestrator fallback + schema_generator | Closed |
| GAP-P1-10 | P1 | Duplicate pricing tables | `energy/pricing.py` delegates to `shared/fabric/llm_pricing` | Closed |
| W5-08 | P2 | Admin per-project inventory | `GET /admin/llm-energy/projects` + EnergySettings table | Closed |
| W7-02 | P2 | SDK invalidation registry | `SDK_LLM_ENERGY_QUERY_PREFIXES` in `@agentstack/react` | Closed |
| GAP-P2-04 | P2 | Semantic cache env-only embed | `context_embedder` + `user_id` on semantic cache | Closed |
| GAP-P2-05 | P2 | Duplicate debit boilerplate | `provider_call_with_debit` helper | Closed |
| GAP-P2-06 | P2 | CAPABILITY_MAP missing LLM energy | Row in §F Economy | Closed |
| GAP-P2-07 | P2 | MCP energy surface | `energy.get_balance`, `energy.list_packs` | Closed |
| BL-02 | P2 | Admin period rollup | `platform_stats` daily + 7d/30d UI | Closed |
| BL-06 | P3 | COGS vs usage_events + CSV export | `reconcile_energy_cogs` + `useAdminExport` | Closed |
| BL-07 | P3 | Usage ledger not wired on LLM paths | `record_llm_usage_evidence` via debit choke | Closed |
| BL-08 | P3 | Admin snapshot JSON download | `exportJson` + EnergySettings button | Closed |
| BL-09 | P3 | Per-project COGS drill-down | `by_project` in `llm_cost_metrics` + inventory | Closed |
| W13-01 | P2 | Finance panel missing llm_payer | `ComputeBudgetSlice` + business home | Closed |
| W13-02 | P2 | Durable per-project COGS | `summarize_platform_payer_by_project` | Closed |
| W13-03 | P2 | Double charge risk on platform payer | `rate_and_record` + `PayerKind.platform` | Closed |
| W14-01 | P2 | SQL USD COGS rollup | `attrs.est_usd` in summarize queries | Closed |
| W14-02 | P2 | Cost explorer LLM COGS endpoint | `/cost/explorer/llm-platform-cogs` | Closed |
| W14-03 | P3 | Finance payer i18n | `formatLlmPayerLabel` + builder keys | Closed |
| W15-01 | P3 | Admin duplicate SQL rollup paths | `build_platform_llm_cogs_summary` in facade | Closed |
| W15-02 | P3 | Inventory period drift (hardcoded 30d) | `period` query + UI sync | Closed |
| BL-01 | P3 | BYOK UX (badge, no purchase CTA) | `canPurchaseEnergyPack` + `LlmRuntimePanel` | Closed |

**Open / follow-up:** none for PP-P1-07.
