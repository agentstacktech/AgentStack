# Gene: core.economy.llm_energy.gen1

**Name:** Platform LLM energy + BYOK monetization  
**Scope:** `shared/atoms/llm_energy.py`, `ai_builder/energy/`, `ai_builder/providers/key_selector.py`, payments `energy_pack`, frontend LLM runtime panels.

## Intent

- **One prepaid SKU:** `builder_energy` — not a second token ticker.
- **Creation over Conflict:** KeySelector + energy service + AgentPay intent; no parallel LLM credit ledger.
- **BYOK honesty:** ecosystem key debits energy; user/project keys do not.

## Related

- `ai_builder.energy.service.balance.management.gen1`
- `ai_builder.providers.key_selector.priority.gen1`
- `repo.platform.unit_economics.gen1`
- ADR `docs/adr/LLM_ENERGY_AND_BYOK.md`
