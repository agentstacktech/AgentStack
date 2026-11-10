# Platform Supply Free.ai — Flow Audit Decomposition v2

**Genes:** `core.llm.partner_providers.gen1` · `core.economy.llm_energy.gen1` · `core.mcp.self_description.gen1`  
**Philosophy gate:** Creation over Conflict · Elegant Minimalism (LOC↓) · Observability first  
**Patterns:** SOLID (one debit choke) · DRY (BUILTIN_AI_PROVIDER_OPTIONS) · ECS (provider components) · KISS · DI (`partner_registry`)

## Product SoT

Free.ai = **platform COGS**: `platform_supply=true`, `debit_energy=true`. Users spend `builder_energy`. Prefer via `builder_config.llm_free_calls`.

## Gap register v2 (this pass)

| ID | Sev | Problem | Fix | Status |
|----|-----|---------|-----|--------|
| P25 | P0 | `reasoning` debit omitted `provider_id` → wrong partner exempt | Pass `provider_id=provider_name` | Closed |
| P26 | P1 | `deduct`/`check_and_reserve` ignored `provider_id` | Thread into `should_debit_energy` | Closed |
| P27 | P1 | Admin Router/Pipeline/Prompt pickers drop freeai | `BUILTIN_AI_PROVIDER_OPTIONS` SoT | Closed |
| P28 | P1 | Duplicate provider label maps (DRY debt) | `providerDisplayName` / options SoT | Closed |
| P29 | P1 | Self-desc `when_blocked` missing Next ladder | `_errors_for` includes `Next:` | Closed |
| P30 | P2 | Enricher missing `related_tools` merge | set related on slice | Closed |
| P31 | P1 | `infer_provider_from_model_id` misses qwen→freeai | Prefix map | Closed |
| F3 | P1 | energy_low peek | mode-gated peek | Closed (prior) |
| P21–P24 | — | purchase opts / provider_id infer / next_actions | prior pass | Closed |

## Detailed TODO with code

### P25 — reasoning debit (SOLID single choke)

**Where:** `agentstack-core/ai_builder/core/features/reasoning.py`  
**Why:** Without `provider_id`, `debit_energy=false` partners still look like ecosystem debit.  
**Code:**

```python
await debit_energy_after_llm_call(..., provider_id=provider_name)
```

### P26 — defense-in-depth on EnergyService

**Where:** `ai_builder/energy/service.py` + `debit.py`  
**Why:** Second gate if call sites forget provider.  
**Code:**

```python
should_debit_energy(key_source, provider_id=provider_id)
```

### P27–P28 — Admin open allowlist (DRY / ECS)

**Where:** `aiKeysShared.ts` → Router / Pipeline / PromptProviders / ModelProviderCard / APIKeysSettings  
**Why:** Closed triples silently drop Free.ai from operator UX.  
**Code:**

```ts
export const BUILTIN_AI_PROVIDER_OPTIONS = [
  { id: 'openai', name: 'OpenAI' },
  /* … */ { id: 'freeai', name: 'Free.ai' },
];
```

### P29–P30 — MCP self-description

**Where:** `shared/mcp/ai_providers_instruction_atoms.py`  
**Why:** Agents need recoverable ladders (`Next: auth.get_profile`).  
**Code:**

```python
out[k] = f"{hint} Next: {nxt}." if nxt else hint
out["related_tools"] = [...]
```

### P31 — model→provider inference

**Where:** `ai_builder/core/constants.py` `infer_provider_from_model_id`  
**Why:** Reasoning / catalog paths with `qwen7b` otherwise mis-route to gemini default.  
**Code:**

```python
if m.startswith("qwen"):
    return "freeai"
```

## Perf / heavy ops (already applied)

| Pattern | Rule |
|---------|------|
| Registry DNA | Bundle cache (`_load_registry_bundle`) — one load for specs+policy |
| energy_low | Peek balance **only** when `mode=energy_low` |
| Catalog list | Derive `providers[]` from `items[]` once |
| Prefer pick | Compute in-process from specs — no extra `preferred_free_provider_id` round-trip in `provider_key` |

## Industry map (reuse, no new services)

| Industry | AgentStack |
|----------|------------|
| LiteLLM default routing | `llm_free_calls.prefer` + `reorder_providers_prefer_free` |
| OpenRouter prepaid | external `debit_energy=false` only |
| Provider self-desc (OpenAPI/MCP) | `ai_providers_instruction_atoms` enricher |

## Remaining ops (not code)

1. Deploy Core → live `ai.providers.*`  
2. Founder `sk-free-…` connect + probe + `agents.run`  

## Philosophy checklist (every step)

| Step | Pillar |
|------|--------|
| One debit choke | Creation over Conflict |
| Delete label triples | Elegant Minimalism |
| next_actions / when_blocked | Observability first |
| No LiteLLM | Elegant Minimalism |
| Leaf treasury writes | `dna_protein_data_plane` |
