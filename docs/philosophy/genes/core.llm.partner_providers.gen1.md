# Gene: core.llm.partner_providers.gen1

**Name:** Partner LLM provider registry (OpenAI-compatible gateways)  
**Scope:** `shared/atoms/llm_partner_provider.py`, `shared/llm/partner_registry.py`, `shared/atoms/treasury_credit.py`, OpenAIProvider `chat_path`, MCP `ai.providers.*`, Agents/Builder catalogs.

## Intent

- **Creation over Conflict:** One KeySelector + one openai-compat adapter + one allowlist SoT.
- **Elegant Minimalism:** No LiteLLM microservice; no second energy ticker; delete duplicate provider frozensets.
- **Observability first:** OpTrace provider_id + debit_energy; usage_ledger partner attrs.
- **Platform supply:** Free.ai backs ecosystem energy (subscriptions + free pool); `platform_supply=true` + `debit_energy=true`. Prefer via `builder_config.llm_free_calls`.

## Related

- `core.economy.llm_energy.gen1`
- `core.agents.fleet.gen1`
- `repo.platform.unit_economics.gen1`
- `ai_builder.providers.key_selector.priority.gen1`
- ADR `docs/adr/PARTNER_LLM_PROVIDER_REGISTRY.md`
- Gap register `docs/ecosystem/PARTNER_LLM_GAP_REGISTER.md`
- Runbook `docs/operations/PARTNER_LLM_FREEAI_RUNBOOK.md`
