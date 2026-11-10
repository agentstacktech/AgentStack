# ADR: Partner LLM Provider Registry

**Status:** Accepted  
**Genetic tag:** `core.llm.partner_providers.gen1`  
**Related:** [LLM_ENERGY_AND_BYOK.md](./LLM_ENERGY_AND_BYOK.md), [UNIT_ECONOMICS_METERING_AND_RATING.md](./UNIT_ECONOMICS_METERING_AND_RATING.md), [USER_AI_KEYS_VS_AGENTSTACK_API_KEYS.md](./USER_AI_KEYS_VS_AGENTSTACK_API_KEYS.md)

## Context

Platform LLM runtime historically hardcoded three providers (`openai`, `anthropic`, `gemini`). Startup grants such as Free.ai (OpenAI-compatible gateways with external prepaid tokens) must plug into Agents Fleet without a second energy ticker or a LiteLLM microservice.

## Decision

1. **One registry SoT:** `builder_config.ai_providers.<id>` (ecosystem + project merge) described by `PartnerProviderSpec` in `shared/atoms/llm_partner_provider.py`.
2. **One allowlist SoT:** `normalize_ai_provider_id` / `merge_provider_id_sets` / `is_ai_provider_id` — no local `frozenset({"openai","anthropic","gemini"})`.
3. **OpenAI-compatible adapters reuse `OpenAIProvider`** with `chat_path` and `supports_responses` (Free.ai: `chat_path=/chat/`, `supports_responses=False`).
4. **Keys** stay in `protected.keys.api.service_keys[<key_slot>]` via KeySelector.
5. **Energy / Free.ai as platform supply:** Free.ai is the default **COGS gateway** for ecosystem-paid calls. Users still spend `builder_energy` (subscription pools + free daily). `spec.platform_supply=true` + `debit_energy=true`. True external partners may set `debit_energy=false` (BYOK prepaid) without becoming platform supply.
6. **Treasury credits** (`data.treasury.credits[]`) remain a human grant inventory; optional `provider_id` + `connection` mirror — not a money ledger (Free.ai grant = COGS inventory).
7. **MCP:** `ai.providers.list|probe|connect|free_calls_set` beside `energy.*`.
8. **No tenant hardcode** (`project_id == 1752` forbidden in shared/core).
9. **Supply prefer leaf:** `builder_config.llm_free_calls` — prefer `platform_supply` providers (Free.ai) for catalogs / Agent defaults / KeySelector fallback (`always` | `energy_low` | `explicit_only`).

## Invariants

- Partner prepaid ≠ `builder_energy`.
- Secrets never in `treasury.*`.
- Leaf DNA writes only for registry / ai_settings.
- Chat-first DoD; image/video partner modalities out of scope until capabilities expand.

## Consequences

- Closed FE/BE allowlists unwind to the shared atoms + registry.
- Agents `GET /llm-providers` returns `providers[]` (compat) + `items[]` (rich).
- Usage ledger records partner calls with `billing_exempt` / `partner` attrs; no PriceBook charge.
