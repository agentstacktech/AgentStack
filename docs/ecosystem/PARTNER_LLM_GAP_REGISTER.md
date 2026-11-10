# Partner LLM — Gap Register

**Genetic tag:** `core.llm.partner_providers.gen1`  
**ADR:** [PARTNER_LLM_PROVIDER_REGISTRY.md](../adr/PARTNER_LLM_PROVIDER_REGISTRY.md)

| ID | Severity | Symptom | Resolution | Status |
|----|----------|---------|------------|--------|
| G1 | P0 | Free.ai `/chat/` vs OpenAI `/chat/completions` | `PartnerProviderSpec.chat_path` + OpenAIProvider | Closed |
| G2 | P0 | `gpt*` forces `/v1/responses` on partners | `supports_responses=False` | Closed |
| G3 | P0 | `project_ai_keys` drops unknown providers | Shared allowlist SoT | Closed |
| G4 | P0 | Closed FE/BE triples | Dynamic ids from registry/atoms | Closed |
| G5 | P1 | False “URL already normalizes” | UT chat_path matrix | Closed |
| G6 | P1 | Buy energy CTA on partner provider | `canPurchaseEnergyPack(…, selectedDebitEnergy)` | Closed |
| G7 | P0 | Ecosystem partner key debits energy | `should_debit_energy(…, spec=)` | Closed |
| G8 | P1 | Orphan `llm.providers` MCP domain | `ai.providers.*` | Closed |
| G9 | P1 | Seed before allowlist open | Phase order + tests | Closed |
| G10 | P2 | Fallback drops freeai | Open `provider_fallback_order` | Closed |
| G11 | P2 | secret_path_registry triple | Dynamic AI secret paths | Closed |
| G12 | P2 | Treasury credits untyped | `treasury_credit` atoms | Closed |
| G13 | P2 | Graph 1752 size | Leaf-only treasury patches | Closed |
| G14 | P2 | PII in treasury.owner | Credits UI excludes owner | Closed |
| G15 | P3 | Modality drift | `capabilities=("chat",)` chat-first | Closed |

## Code-reduction note (P7)

Closed frozenset / triple allowlists replaced by `shared.atoms.llm_partner_provider` + `partner_registry`. Net: one SoT; deleted duplicate `_VALID_PROVIDERS` / `_PROVIDERS` triples in project keys, user settings, secret_path_registry, FE `AiProviderId`, admin Models/APIKeys.

## P8 ops status

| Step | Status |
|------|--------|
| Ecosystem `builder_config.ai_providers.freeai` leaf | Done (pid=1, platform_supply) |
| Treasury 1752 Free.ai `provider_id` + `connection` | Done (`key_present=false`) |
| Core deploy with `ai.providers.*` | Pending deploy |
| Founder `sk-free-…` connect + probe + `agents.run` | Pending key + deploy |

## Flow audit 2026-11-10 (platform supply)

| ID | Sev | Status |
|----|-----|--------|
| F1 Fleet double debit | P0 | Closed — end-of-run consume no-op; per-call SoT |
| P16 Admin wipe freeai | P0 | Closed — open-map merge |
| F9 Policy DNA reload | P1 | Closed — specs+policy bundle cache |
| F5 Project connect cache | P1 | Closed |
| F6/P17 Self-desc stale copy | P1 | Closed — ai_providers_instruction_atoms |
| F7 platform_supply ledger | P1 | Closed |
| F12/F13 catalog DRY | P2 | Closed |
| P20 Zod platform_supply | P1 | Closed |
| P19 FE invalidate | P1 | Closed |
| F3 energy_low balance | P1 | Closed — peek only when mode=energy_low |
| P21 selectedDebitEnergy | P1 | Closed — AgentSpecEnergyForm + canPurchaseEnergyPack opts |
| P22 provider_call provider_id | P1 | Closed — _infer_provider_id on debit choke |
| P24 self-desc next_actions | P2 | Closed — ai_providers_instruction_atoms |
| P23 treasury leaf R/W | P2 | Closed — dna_get/set_project_data_path on credits |
| P25 reasoning missing provider_id | P0 | Closed — debit passes provider_name |
| P26 EnergyService provider_id gate | P1 | Closed — deduct/check_and_reserve |
| P27 Admin pickers drop freeai | P1 | Closed — BUILTIN_AI_PROVIDER_OPTIONS |
| P28 Duplicate label maps | P1 | Closed — providerDisplayName SoT |
| P29 when_blocked Next ladder | P1 | Closed — instruction atoms |
| P30 related_tools enrich | P2 | Closed |
| P31 qwen→freeai infer | P1 | Closed — infer_provider_from_model_id |
