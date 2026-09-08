# Sandbox API

REST surface for tenant 8DNA environments: fork, checkpoint, promote, canary rollout, A/B tests, limits.

**Concepts:** [SANDBOX_AND_ENVIRONMENTS.md](../SANDBOX_AND_ENVIRONMENTS.md) · [SANDBOX_PLAYGROUND_GUIDE.md](../SANDBOX_PLAYGROUND_GUIDE.md)  
**Canary traffic plane:** documented in platform operator ADRs (not mirrored here).  
**OpenAPI:** [Swagger](https://agentstack.tech/swagger) → tag **Sandbox**

## Base path

`/api/sandbox/*` — all routes require authentication; pass `project_id` as query param unless noted.

## Promote strategy (SB-G9)

`POST /api/sandbox/promote?project_id={pid}`

```json
{
  "env_uuid": "<anchor_uuid>",
  "strategy": "immediate"
}
```

| `strategy` | Behavior |
|------------|----------|
| `immediate` | Swap `active_env_uuid` to candidate; `status=live` |
| `canary` | `status=rolling_out`; set `traffic_weight` to first rollout step; **no** pointer swap until 100% |
| `blue_green` | Approval gates; atomic swap when approved |

Legacy `instant: true|false` is accepted as alias: `true` → `immediate`, `false` → `blue_green`.

## Canary rollout

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/rollout/advance` | `{ "env_uuid" }` — next step; at 100% swaps prod pointer |
| POST | `/rollout/abort` | `{ "env_uuid" }` — restore `previous_active_env_uuid`, zero traffic |

## Environment context

Send `X-AgentStack-Env: <anchor_uuid>` (or `?env=<name>`) on API/MCP requests to scope DNA writes to a sandbox generation.

Optional **read** traffic split: set `project.data.config.canary_auto_route=true` (default `false`). Only `GET`/`HEAD`/`OPTIONS` participate; mutations without explicit header stay on production.

## Hosting bridge (static `/s/`)

Canonical `/s/{pid}/{name}/` serves live bytes only. From sandbox:

`POST /api/sandbox/hosting/release?project_id={pid}` → `release_uuid` + `preview_url`; then `POST /api/hosting/releases/{uuid}/promote`.

MCP: `generation.hosting.release` → `hosting.release.promote`.

## SDK

```typescript
import { getSDK } from '@agentstack/sdk';

const sdk = getSDK();
sdk.sandbox.setSandboxEnv(anchorUuid);
await sdk.sandbox.promote(projectId, { env_uuid, strategy: 'canary' });
await sdk.sandbox.promoteHostingRelease(projectId, { site_id });
```

## Limits

`GET /api/sandbox/limits?project_id={pid}` — tier + buffs merged limits, usage counters, `features.{canary,shadow_writes,ab_test}`.

**Quota:** `usage.environment_count` counts open generations only (`draft|validating|ready|rolling_out`). Live/archived do not consume the plan slot.

**403 plan/limit body** (fork / promote / checkpoint):

```json
{
  "error": "sandbox_limit_exceeded",
  "limit_type": "sandbox_environment_count",
  "current": 1,
  "max": 1,
  "available_from": "basic",
  "upgrade_url": "/pricing"
}
```

Ladder: Free 0 · Starter 1 · Basic 3 · Pro 5 — [SANDBOX_GENERATION_LIMITS_DECOMPOSITION.md](../plans/SANDBOX_GENERATION_LIMITS_DECOMPOSITION.md).
