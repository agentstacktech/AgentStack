# Partner LLM — Free.ai runbook

**Genetic tag:** `core.llm.partner_providers.gen1`  
**ADR:** [PARTNER_LLM_PROVIDER_REGISTRY.md](../adr/PARTNER_LLM_PROVIDER_REGISTRY.md)

## Not deploy.full

Do **not** add Free.ai heal to deploy presets. Platform ships registry code; keys + treasury credit links are operator MCP / Settings.

## Ladder

1. `auth.get_profile` — `session_ready`
2. **Deploy** Core with `mcp/tools_ai_providers.py` so `ai.providers.*` appears in discovery (pre-deploy: `action_not_found`)
3. Ensure ecosystem `builder_config.ai_providers.freeai` (builtin seed + optional leaf patch on pid=1)
4. Founder: `ai.providers.connect` with `provider_id=freeai`, `api_key=sk-free-…` (ecosystem or project scope)
5. `ai.providers.probe` — expect `ok`
6. Tenant (e.g. 1752): leaf-patch `treasury.credits[]` row `service=Free.ai` → `provider_id=freeai` + `connection` (no secrets; no owner PII)
7. Smoke: AgentSpec `llm.provider=freeai`, `model=qwen7b`, `preflight_required=true` → `agents.run`
8. Confirm OpTrace: `builder_energy` **is** debited; usage attrs `platform_supply=true` (not partner/billing_exempt)

## MCP examples

```json
{"action": "ai.providers.list", "params": {}}
{"action": "ai.providers.connect", "params": {"provider_id": "freeai", "api_key": "sk-free-…", "scope": "ecosystem"}}
{"action": "ai.providers.probe", "params": {"provider_id": "freeai", "model": "qwen7b"}}
```

## Chat URL

Free.ai uses `https://api.free.ai/v1` + `chat_path=/chat/` (not `/chat/completions`).

## Platform supply (Free.ai → energy)

Free.ai tokens are **platform COGS**. Users get inference via `builder_energy` (subscriptions + free daily pool). Ecosystem prefers Free.ai when `llm_free_calls.prefer` is on; energy is still debited.

Leaf: `builder_config.llm_free_calls` on pid=1 (project may override).  
Provider seed: `builder_config.ai_providers.freeai` with `platform_supply=true`, `debit_energy=true`.

| Field | Meaning |
|-------|---------|
| `enabled` | Allow auto-routing to platform_supply providers |
| `prefer` | Rank Free.ai first in catalogs / Agent defaults / KeySelector fallback |
| `mode` | `always` \| `energy_low` \| `explicit_only` |
| `energy_threshold` | When `energy_low`, prefer supply if pool ≤ threshold |
| `provider_ids` | Optional allowlist (default: all `platform_supply`) |

Admin UI: Builder → Energy → **Platform LLM supply (Free.ai)**.  
MCP (after deploy): `ai.providers.free_calls_set` / policy on `ai.providers.list`.

```json
{"action": "ai.providers.free_calls_set", "params": {"enabled": true, "prefer": true, "mode": "always"}}
```

## Acceptance blockers (ops)

- Live `ai.providers.*` requires Core deploy with the partner MCP module.
- Connect/probe/`agents.run` freeai smoke requires founder `sk-free-…` (never commit the key).
- Treasury `provider_id` link can land before the key (key_present=false until connect).
