# LLM Energy Operations Runbook

**Genetic tag:** `core.economy.llm_energy.gen1`  
**Gap register:** [LLM_ENERGY_GAP_REGISTER.md](../ecosystem/LLM_ENERGY_GAP_REGISTER.md)

## Quick checks

1. **Balance:** `GET /api/energy/balance?project_id={pid}` — project pool when scoped.
2. **Packs:** `GET /api/energy/packs` — tier → units from `builder_config.energy.pack_prices`.
3. **Grant after card:** payment webhook `intent=energy_pack` → `grant_energy_pack_from_payment` (idempotency `fiat:{payment_id}`).
4. **BYOK:** OpTrace `key_source` ≠ `ecosystem` → no `builder_energy` debit.

## Env tunables

| Variable | Default | Purpose |
|----------|---------|---------|
| `LLM_ECOSYSTEM_KEY_MODE` | `optional` | Documented; strict mode blocks ecosystem payer when platform key missing |

## Admin scale (SoT)

- FREE daily pool ≈ **8 energy** (~20 typical 4k-token calls).
- Pack tiers: small **50**, medium **200**, large **500** energy.
- Python `AIBuilderEnergyService` and Builder `useBuilderConfig` defaults must match.

## Troubleshooting

| Symptom | Check |
|---------|--------|
| Card paid, no energy | Payment TX metadata `pack_tier` (not raw `100`); webhook logs `energy_pack_granted` |
| BYOK still debited | `llm_payer` + `key_source` in OpTrace; single debit in `call_ai_provider` |
| Wallet KPI wrong pool | Finance facade uses `project_id` on `get_energy_balance` |
| Unpaid grant in prod | `POST /energy/purchase-pack` returns **402** `payment_required` |

## Business management

- **Finance hub:** `project_finance_read_facade` → `compute_budget` (`builder_energy_*`, `llm_payer`, `keys_status`).
- **Cost explorer:** `GET /api/cost/explorer/llm-platform-cogs?since_days=7` — durable SQL rollup (`event_count`, `est_usd_total` from `attrs.est_usd`).
- **Admin:** `builder/energy` inventory — `durable_cogs_calls` / `durable_cogs_usd` per project; `period` matches snapshot toggle (7d/30d).
- **Tenant UX:** BYOK mode shows badge; pack purchase only when `llm_payer=ecosystem` (`canPurchaseEnergyPack`).

## Tests

```bash
npm run test:scope -- --gene core.economy.llm_energy.gen1
pytest agentstack-core/tests/test_mentor_channel_energy.py -q
```
