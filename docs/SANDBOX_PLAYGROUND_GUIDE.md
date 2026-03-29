# 8DNA Sandbox & Playground — Developer Guide

**Environments · Checkpoints · A/B Tests · Canary Rollouts · Promotion Workflow · Targeting Segments**

> **Core idea:** **Environments are generations.** A sandbox is not a full clone of production — it is a derived line of changes on top of a parent state. Production is generation `0`; each new environment is the next generation in that line.

See also: [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md). **Web users:** [USER_FEATURES_GUIDE.md](USER_FEATURES_GUIDE.md).

---

## Contents

1. [Core Concepts](#core-concepts)
2. [Architecture — Pure 8DNA](#architecture--pure-8dna)
3. [Subscription Limits](#subscription-limits)
4. [REST API Reference](#rest-api-reference)
5. [Flow Diagrams](#flow-diagrams)
6. [Generation Resolver & Cache](#generation-resolver--cache)
7. [A/B Tests](#ab-tests)
8. [Canary Rollout](#canary-rollout)
9. [Promotion Workflow](#promotion-workflow)
10. [Targeting Segments](#targeting-segments)
11. [Shadow Writes](#shadow-writes)
12. [Protected Field Security](#protected-field-security)
13. [Frontend Components](#frontend-components)
14. [Code Examples](#code-examples)

---

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Environment** | An isolated context with its own `generation` number. Production is always `generation=0`. |
| **Generation** | The biological layer of a 8DNA entity. Every fork increases generation by 1. |
| **parent_uuid** | UUID pointer to the parent entity from which a diff was created. Forms the lineage chain. |
| **Diff entity** | A sandbox entity that stores only *changed* fields (not the full snapshot). |
| **Checkpoint** | A fully materialised merge of the entire ancestor chain — a snapshot frozen in time. |
| **Environment Anchor** | A `data_projects_project` entity with `entity_type=environment_anchor` that controls an environment. |
| **Head pointer** | `data.config.head_uuid` — always points to the latest diff in the environment. |
| **Pinned pointer** | `data.config.pinned_uuid` — immutable snapshot UUID used for share / review links. |

### Environment State Machine

```
draft → validating → ready → rolling_out → live → archived
```

| State | Meaning |
|-------|---------|
| `draft` | Just created, not yet validated |
| `validating` | Pre-promotion checks running |
| `ready` | Passed all checks, ready to promote |
| `rolling_out` | Canary traffic ramp-up in progress |
| `live` | Fully promoted to production |
| `archived` | Inactive; TTL cleanup eligible |

---

## Architecture — Pure 8DNA

The sandbox system requires **no new database tables**. All state lives inside existing 8DNA entities:

```
data_projects_project    ← environment anchors (entity_type=environment_anchor)
data_projects_backup     ← A/B test records + PromotionRequest entities
data_projects_user       ← forked user entities for segment-based tests
```

### data.config fields on an environment anchor

| Field | Type | Description |
|-------|------|-------------|
| `env_type` | `"production"\|"sandbox"\|"staging"\|"ab_test"\|"feature"` | Environment type |
| `env_name` | `string` | Human-readable label |
| `is_checkpoint` | `bool` | True = materialised full-merge snapshot |
| `diff_only` | `bool` | True = entity stores only changed fields |
| `status` | state machine value | See state machine above |
| `head_uuid` | `UUID` | Pointer to latest diff |
| `pinned_uuid` | `UUID` | Immutable review snapshot |
| `traffic_weight` | `0–100` | Canary traffic percentage |
| `rollout_steps` | `array` | Stepwise canary config |
| `ab_seed` | `string` | Stable bucketing seed for A/B split |
| `segments` | `array` | Reusable targeting segments |
| `shadow_writes` | `bool` | Mirror production writes into this env |
| `expires_at` | ISO datetime | TTL — env auto-archived after this |

### Diff Storage Model

```
Production entity (generation=0, parent_uuid=null)
        ↓ fork
Sandbox diff entity (generation=1, parent_uuid=<prod_uuid>)
        ↓ accumulates changes
Checkpoint (generation=2, is_checkpoint=true — full merged data)
```

`GenerationResolver` walks the chain and deep-merges JSONB from root to target:

```python
resolved = await generation_resolver.resolve(
    target_uuid="<env_uuid>",
    table="data_projects_project",
    project_id=42,
)
```

Results are cached in `NeuralCacheEngine` (namespace `generation:resolve:project:{id}`, TTL 300 s, tag `generation_chain:{root_uuid}` for invalidation).

---

## Subscription Limits

| Limit | Free | Starter | Basic | Pro | Premium | Enterprise |
|-------|------|---------|-------|-----|---------|------------|
| Environments | 0 | 1 | 3 | 10 | 30 | Unlimited |
| Generation depth | 0 | 3 | 5 | 10 | 20 | Unlimited |
| Checkpoints | 0 | 1 | 3 | 10 | 30 | Unlimited |
| A/B tests | 0 | 0 | 1 | 5 | 20 | Unlimited |
| Shadow writes | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ |
| Env TTL (days) | 0 | 7 | 14 | 30 | 90 | No expiry |

> **Free plan:** Backup and rollback of production entities only. No sandbox environments.
>
> **Starter plan:** 1 sandbox environment, 3 generations deep. Suitable for solo devs testing a feature branch.

---

## REST API Reference

All endpoints are under `/api/sandbox`. Auth: project API key or user JWT in `Authorization: Bearer <token>`.

### Environment Management

| Method | Path | Description | Auth |
|--------|------|-------------|------|
| `POST` | `/api/sandbox/fork` | Fork production project into a new environment | 🔑 JWT |
| `GET` | `/api/sandbox/environments` | List all environments for a project | 🔑 |
| `DELETE` | `/api/sandbox/environment` | Archive and cleanup an environment | 🔑 JWT |

#### POST /api/sandbox/fork

```json
{
  "source_project_id": 42,
  "env_name": "feature/payments-v2",
  "env_type": "sandbox",
  "user_filter": null,
  "segment_id": null
}
```

Response:

```json
{
  "anchor_uuid": "...",
  "fork_project_uuid": "...",
  "env_name": "feature/payments-v2",
  "env_type": "sandbox",
  "generation": 1,
  "forked_count": 3,
  "manifest_uuid": "...",
  "status": "draft"
}
```

### Checkpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/checkpoint` | Materialise a full-merge snapshot |
| `POST` | `/api/sandbox/rollback` | Branch a new environment from a checkpoint |

#### POST /api/sandbox/checkpoint

```json
{
  "env_uuid": "<anchor_uuid>",
  "label": "before-payment-migration"
}
```

### Promotion

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/promote` | Initiate promotion (creates PromotionRequest entity) |
| `POST` | `/api/sandbox/approve` | Approve a pending PromotionRequest (Enterprise) |

#### POST /api/sandbox/promote

```json
{
  "env_uuid": "<anchor_uuid>",
  "strategy": "immediate"
}
```

Strategies: `immediate`, `canary`, `blue_green`.

Pre-promotion checks run automatically:

| Check | What it validates |
|-------|-----------------|
| `schema_valid` | GenerationResolver chain resolves without error |
| `depth_ok` | Ancestor chain ≤ 10 generations |
| `conflict_free` | No other environment currently in `rolling_out` state |
| `health_ok` | Env status is `ready`, `validating`, or `live` |

If any check fails, promotion is blocked and the response contains `checks` with per-check booleans.

### Canary Rollout

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/canary/advance` | Advance to next rollout step |
| `POST` | `/api/sandbox/canary/abort` | Abort canary — rollback to previous traffic weight |

#### POST /api/sandbox/canary/advance

```json
{ "env_uuid": "<anchor_uuid>" }
```

### Snapshots & Tree

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/pin` | Create a pinned immutable snapshot UUID |
| `GET` | `/api/sandbox/tree` | Return full generation tree for visualisation |
| `GET` | `/api/sandbox/diff` | JSON diff between two entity states |
| `POST` | `/api/sandbox/protected-check` | Server-side hash-check for a protected field |

#### GET /api/sandbox/diff

```
GET /api/sandbox/diff?uuid_a=<prod_uuid>&uuid_b=<env_uuid>&table=data_projects_project&project_id=42
```

Response:

```json
{
  "added":    { "config.new_feature": true },
  "removed":  {},
  "modified": { "config.payment_provider": { "old": "stripe", "new": "tochka" } }
}
```

### A/B Tests

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/ab-test` | Create a new A/B test |
| `GET` | `/api/sandbox/ab-test/{test_id}/metrics` | Get live split metrics |
| `GET` | `/api/sandbox/ab-test/{test_id}/variant` | Get variant assignment for a user |
| `POST` | `/api/sandbox/ab-test/{test_id}/result` | Record a result event |
| `POST` | `/api/sandbox/ab-test/{test_id}/promote` | Declare winner and promote |

#### POST /api/sandbox/ab-test

```json
{
  "env_a_uuid": "<control_uuid>",
  "env_b_uuid": "<variant_uuid>",
  "split_pct": 50,
  "metric": "conversion_rate"
}
```

#### GET /api/sandbox/ab-test/{test_id}/metrics

```json
{
  "test_id": "...",
  "env_a": { "requests": 1200, "conversions": 84, "rate": 7.0 },
  "env_b": { "requests": 1195, "conversions": 107, "rate": 8.95 },
  "winner": null,
  "status": "active"
}
```

### Segments

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/sandbox/segment` | Create a reusable targeting segment |
| `GET` | `/api/sandbox/segments` | List all segments for a project |

#### POST /api/sandbox/segment

```json
{
  "name": "premium-users",
  "rules": [
    { "field": "subscription_tier", "op": "eq", "value": "premium" },
    { "field": "country", "op": "in", "value": ["US", "DE", "GB"] }
  ]
}
```

---

## Flow Diagrams

### Fork Flow (transactional order)

```
1. VALIDATE    — subscription limits check (SandboxSubscriptionGuard)
2. ANCHOR      — create environment_anchor entity (draft status)
3. PROJECT     — fork project entity as diff-child (generation=N+1)
4. USERS       — fork filtered users (segment or user_filter)
5. SUB-ENTITIES— fork any additional tracked entities
6. MANIFEST    — create manifest backup record for audit
7. CACHE       — invalidate GenerationResolver cache for root chain
```

All steps run inside a single DB transaction. Failure at any step triggers a full rollback.

### Checkpoint Flow

```
1. Resolve full merged state via GenerationResolver
2. Insert new entity: is_checkpoint=true, diff_only=false, full data
3. Update anchor's head_uuid to checkpoint UUID
4. Invalidate GenerationResolver cache
```

### Promotion Flow

```
1. Run pre-promotion checks (schema, depth, conflict, health)
2. Create PromotionRequest entity in data_projects_backup
3. If strategy=canary → status=rolling_out, traffic_weight=first_step
4. If strategy=immediate → status=live, swap prod pointer
5. Enterprise: require approval via /api/sandbox/approve
6. On approve → swap generation pointers in production
```

---

## Generation Resolver & Cache

`GenerationResolver` is the core service that reconstructs the full state of any entity at any generation by walking the `parent_uuid` chain and deep-merging JSONB.

### Cache Strategy

- **Namespace:** `generation:resolve:project:{project_id}`
- **Key:** `resolve:{root_uuid}:{target_uuid}`
- **Tag:** `generation_chain:{root_uuid}` — invalidated on any write to the chain
- **TTL:** 300 seconds

### Usage in code

```python
from services.generation_resolver import generation_resolver, invalidate_generation_cache

# Resolve full merged state
data = await generation_resolver.resolve(
    target_uuid="<uuid>",
    table="data_projects_project",
    project_id=42,
    use_cache=True,
)

# Get ancestor chain
chain = await generation_resolver._get_ancestor_chain(target_uuid, table)

# Invalidate after write
await invalidate_generation_cache(root_uuid, project_id)

# Diff two versions
diff = await generation_resolver.diff(uuid_a, uuid_b, table, project_id)

# Full descendant tree for UI
tree = await generation_resolver.get_generation_tree(root_uuid, table, project_id)
```

---

## A/B Tests

A/B tests use **stable bucketing** (LaunchDarkly pattern): `hash(user_uuid + ab_seed) % 100`. The same user always gets the same variant, even across server restarts or horizontal scaling.

```python
from services.ab_test_service import ab_test_service

# Create test
test = await ab_test_service.create_ab_test(
    env_a_uuid="<control>",
    env_b_uuid="<variant>",
    project_id=42,
    user_id=1,
    split_pct=50,
    metric="conversion_rate",
)

# Get variant for a specific user
variant = await ab_test_service.get_variant_for_user(
    test_id=test["test_id"],
    user_uuid="<user_uuid>",
    project_id=42,
)
# → "A" or "B"

# Record result
await ab_test_service.record_result(
    test_id=test["test_id"],
    user_uuid="<user_uuid>",
    project_id=42,
    outcome="converted",
)

# Get live metrics
metrics = await ab_test_service.get_metrics(test["test_id"], 42)
```

---

## Canary Rollout

Canary rollout gradually shifts production traffic to the new environment via `traffic_weight` (0–100). Steps are defined in `rollout_steps` on the anchor's config.

### Default rollout steps

```json
[
  { "weight": 5,  "pause_minutes": 30 },
  { "weight": 20, "pause_minutes": 60 },
  { "weight": 50, "pause_minutes": 120 },
  { "weight": 100, "pause_minutes": 0 }
]
```

```python
# Advance to next step
result = await sandbox_service.advance_canary_step(env_uuid, project_id)

# Abort and rollback
result = await sandbox_service.abort_canary(env_uuid, project_id)
```

---

## Promotion Workflow

The `PromotionRequest` entity (stored in `data_projects_backup`, `entity_type=promotion_request`) provides a full audit trail for every promotion:

```json
{
  "env_uuid": "...",
  "strategy": "canary",
  "status": "pending_approval",
  "checks": {
    "schema_valid": true,
    "depth_ok": true,
    "conflict_free": true,
    "health_ok": true
  },
  "approvers": [],
  "approved_at": null,
  "created_at": "2026-03-25T12:00:00Z"
}
```

**Enterprise only:** Promotion requires explicit `/api/sandbox/approve` before traffic is switched.

---

## Targeting Segments

Segments are stored as rules inside `project.data.config.segments[]` and evaluated by `SegmentEvaluator`.

### Supported operators

| Operator | Meaning |
|----------|---------|
| `eq` | Field equals value |
| `neq` | Field does not equal value |
| `in` | Field is in list |
| `not_in` | Field is not in list |
| `gt` / `lt` | Greater / Less than (numeric) |
| `contains` | Field string contains value |

### Example: fork with segment

```bash
POST /api/sandbox/fork
{
  "source_project_id": 42,
  "env_name": "premium-test",
  "env_type": "sandbox",
  "segment_id": "<segment_uuid>"
}
```

Only users matching the segment rules are forked into the sandbox.

---

## Shadow Writes

When `data.config.shadow_writes=true` on an environment anchor, every production write to tracked entities is asynchronously mirrored as a diff-patch into the sandbox. The production response is **never delayed** — mirroring is fire-and-forget via `asyncio.create_task`.

**Available from Pro plan.**

To enable:

```bash
POST /api/sandbox/fork
{
  "source_project_id": 42,
  "env_name": "staging",
  "env_type": "staging"
}
```

Then in your production write handler:

```python
from shared.middleware.shadow_write import shadow_writer

# After successful production write
await shadow_writer.mirror_write(
    entity_uuid=entity.uuid,
    table="data_projects_project",
    diff_data={"some_field": new_value},
    project_id=42,
)
```

---

## Protected Field Security

Sandbox environments **never expose decrypted secrets**. The `EnvironmentRouter` middleware blocks any request with `?decrypt_protected=true` in a sandbox context — returning `403 sandbox_secret_access_denied`.

Instead, use server-side hash verification:

```bash
POST /api/sandbox/protected-check
{
  "env_uuid": "<anchor_uuid>",
  "field_path": "keys.api.payment_secret",
  "value": "sk_test_my_key"
}
```

Response:

```json
{ "match": true }
```

The secret never leaves the server. The response only confirms whether the provided value matches the stored hash.

### How it works

1. Client sends the plaintext value to verify
2. Server reads the protected field from the nearest ancestor (GenerationResolver)
3. Server compares `sha256(stored_value) == sha256(provided_value)`
4. Returns `{match: bool}` only

---

## Frontend Components

All components are under `src/components/sandbox/`:

| Component | Description |
|-----------|-------------|
| `EnvironmentSwitcher.tsx` | Header dropdown — switch between active environments |
| `GenerationTree.tsx` | Git-graph style tree: checkpoints, diffs, status pills, action buttons |
| `DiffViewer.tsx` | JSON diff display with added/modified/removed highlighting |
| `ABTestDashboard.tsx` | A/B test creation, live split metrics, winner promotion |
| `CanaryControls.tsx` | Canary rollout: traffic weight, step advance, abort |
| `PromotionRequestFlow.tsx` | Promotion request status, validation checklists, approver badges |
| `SegmentBuilder.tsx` | Create and edit targeting segments with rule editor |
| `EnvBadge.tsx` | Environment type badge + `SandboxLimitBanner` + `SandboxLimitsWidget` |

### Main Panel

`src/modules/sandbox/SandboxPanel.tsx` — tabbed control panel:

- **Overview** — environment list, status, quick actions
- **Tree** — generation tree visualisation
- **Diff** — compare any two versions
- **A/B** — A/B test dashboard
- **Canary** — canary rollout controls
- **Segments** — reusable targeting segment editor

---

## Code Examples

### React hooks

```typescript
import {
  useSandboxEnvironments,
  useForkProject,
  useCheckpoint,
  usePromoteEnvironment,
  useABTestMetrics,
  useAdvanceCanary,
} from '@/hooks/useSandbox'

// List environments
const { data: envs } = useSandboxEnvironments(projectId)

// Fork
const fork = useForkProject()
await fork.mutateAsync({ source_project_id: 42, env_name: 'my-feature', env_type: 'sandbox' })

// Checkpoint
const checkpoint = useCheckpoint()
await checkpoint.mutateAsync({ env_uuid, label: 'v1' })

// A/B metrics
const { data: metrics } = useABTestMetrics(testId, projectId)
```

### Environment context header

All requests from a sandbox client should include:

```http
X-AgentStack-Env: feature/payments-v2
```

The `EnvironmentRouter` middleware resolves this to a `SandboxContext` and injects `generation` and `env_type` into every downstream `DNAFilter` automatically.

Alternatively, set the user's active env in their session:

```json
{ "data": { "sandbox": { "active_env": "<env_uuid_or_name>" } } }
```

---

## FAQ

**Q: Does forking copy production data completely?**  
A: No. Fork creates diff-children that are almost empty. Full data is resolved on-demand by `GenerationResolver` by merging the ancestor chain. This means a fork is extremely cheap — only the changed fields are stored in the sandbox.

**Q: Are protected fields (API keys, secrets) copied to sandboxes?**  
A: No. Protected fields are never copied during a fork. `GenerationResolver` always reads them from the nearest ancestor that has them. In sandbox context, `?decrypt_protected=true` is blocked — use `/api/sandbox/protected-check` instead.

**Q: How is A/B user assignment stable?**  
A: Via stable bucketing: `hash(user_uuid + ab_seed) % 100`. The `ab_seed` is generated once at test creation and never changes. The same user always gets the same variant.

**Q: What happens if an env exceeds its TTL?**  
A: `cleanup_expired_environments()` is run periodically. Expired environments are archived (status=archived) and their diff-children are cleaned up. Production data (generation=0) is never touched.

**Q: What does the canary `traffic_weight` actually control?**  
A: It's an advisory weight stored in the env anchor's config. Your routing layer (API gateway, EnvironmentRouter middleware, or your own code) reads `traffic_weight` from the `SandboxContext` to decide what percentage of users should see the new version.
