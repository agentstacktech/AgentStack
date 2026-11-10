# ADR: LLM Energy and BYOK

**Status:** Accepted  
**Genetic tag:** `core.economy.llm_energy.gen1`  
**Related:** [UNIT_ECONOMICS_METERING_AND_RATING.md](./UNIT_ECONOMICS_METERING_AND_RATING.md), [USER_AI_KEYS_VS_AGENTSTACK_API_KEYS.md](./USER_AI_KEYS_VS_AGENTSTACK_API_KEYS.md)

## Context

Platform LLM monetization uses prepaid **energy** (`builder_energy` on 8DNA) when the **ecosystem** API key is used. Users may bring their own keys (BYOK) stored in `protected.keys.api.service_keys`.

## Decision

### Five SoT (extends unit economics ADR)

| Contour | SoT |
|---------|-----|
| Policy | `data.ai_settings.llm_payer` + KeySelector |
| Customer prepaid pool | `builder_energy` (user or project slice) |
| Physical LLM evidence | OpTrace + usage ledger |
| Provider COGS | `shared/fabric/llm_pricing.py` |
| Customer invoice | **Not** PriceBook per LLM call when energy prepaid |

### Payer policy

- `ecosystem` — platform key; debit energy pool.
- `project_byok` / `user_byok` — BYOK only; **no** silent fallback to ecosystem; **no** energy debit.

### Debit rule

`should_debit_energy(key_source)` is true only when `key_source == "ecosystem"`.

### Payments

`intent=energy_pack` grants via `add_purchased_energy` with idempotency `fiat:{payment_id}`. Metadata uses `pack_tier: small|medium|large` and optional `project_id`.

### No dual charge

Usage events with ecosystem key set `billing_exempt` — skip `rate_and_record` for `profile.ai.llm.metered`.

## Consequences

- Project wave 1 stores `builder_energy` on project slice (`user_id=0`).
- No new SQL tables; DNA leaf writes only.
