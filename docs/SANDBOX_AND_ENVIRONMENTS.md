# Sandbox & Environments — Developer Guide

**Environments · Checkpoints · A/B Tests · Canary Rollouts · Promotion Workflow · Targeting Segments**

## Overview

AgentStack lets you fork project data into **isolated environments** (sandboxes, staging, A/B variants). Each environment has a **generation** number: production is `generation=0`; each fork increments generation. Sandboxes store **diffs** (changed fields only) on top of a **parent** entity; full state is **resolved** by merging the ancestor chain. That keeps forks cheap and makes checkpoints and rollbacks predictable.

**Related:** [OPENAPI.md](OPENAPI.md) (Swagger, schema, `/api-docs`) · [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) (API keys, roles, and Field Access Policy) · Subscription limits below.

---

## Contents

1. [Core Concepts](#core-concepts)
2. [Architecture](#architecture)
3. [Subscription Limits](#subscription-limits)
4. [REST API Reference](#rest-api-reference)
5. [Flow Diagrams](#flow-diagrams)
6. [Merge resolution & caching](#merge-resolution--caching)
7. [A/B Tests](#ab-tests)
8. [Canary Rollout](#canary-rollout)
9. [Promotion Workflow](#promotion-workflow)
10. [Targeting Segments](#targeting-segments)
11. [Shadow Writes](#shadow-writes)
12. [Protected Field Security](#protected-field-security)
13. [Dashboard UI (product)](#dashboard-ui-product)
14. [Code Examples](#code-examples)
15. [FAQ](#faq)

---

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Environment** | An isolated context with its own `generation` number. Production is always `generation=0`. |
| **Generation** | Version layer of an entity. Each fork increases generation by 1. |
| **parent_uuid** | UUID pointer to the parent entity from which a diff was created. Forms the lineage chain. |
| **Diff entity** | A sandbox entity that stores only *changed* fields (not the full snapshot). |
| **Checkpoint** | A fully materialised merge of the entire ancestor chain — a snapshot frozen in time. |
| **Environment anchor** | The project-side record that owns environment metadata (name, status, pointers to head/pinned UUIDs). |
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

## Architecture

Environment state is stored in **project JSON (8DNA)** — anchors, diffs, and related metadata live alongside your normal project data.

```
Project records     ← environment anchors and forked project state
Audit / backup area ← promotion requests, A/B metadata (implementation detail)
Per-user copies     ← optional forked user rows for segment-based tests
```

### `data.config` fields on an environment anchor

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

The platform **walks the `parent_uuid` chain** and **deep-merges** JSON from root to the target UUID (so a sandbox row only stores overrides). Merged results are **cached briefly** on the server to keep repeated reads fast; cache is invalidated when the chain changes.

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
> **Starter plan:** 1 sandbox environment, 3 generations deep. Suitable for solo developers testing a feature branch.

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
| `POST` | `/api/sandbox/promote` | Initiate promotion (creates a promotion request record) |
| `POST` | `/api/sandbox/approve` | Approve a pending promotion request (Enterprise) |

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
| `schema_valid` | Ancestor chain merges without error |
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
GET /api/sandbox/diff?uuid_a=<prod_uuid>&uuid_b=<env_uuid>&project_id=42&table=<entity_kind>
```

Use the `table` value required by your deployment (see [Swagger UI](https://agentstack.tech/swagger) or [OPENAPI.md](OPENAPI.md) — it identifies which root entity type to compare).

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
1. VALIDATE    — subscription limits
2. ANCHOR      — create environment anchor (draft status)
3. PROJECT     — fork project row as diff-child (generation=N+1)
4. USERS       — fork filtered users (segment or user_filter)
5. SUB-ENTITIES— fork any additional tracked entities
6. MANIFEST    — audit record for the operation
7. CACHE       — invalidate merge cache for the chain
```

All steps run inside a single database transaction. Failure at any step triggers a full rollback.

### Checkpoint Flow

```
1. Resolve full merged state (ancestor chain)
2. Insert new row: is_checkpoint=true, diff_only=false, full data
3. Update anchor's head_uuid to checkpoint UUID
4. Invalidate merge cache for the chain
```

### Promotion Flow

```
1. Run pre-promotion checks (schema, depth, conflict, health)
2. Create promotion request record (audit trail)
3. If strategy=canary → status=rolling_out, traffic_weight=first_step
4. If strategy=immediate → status=live, swap prod pointer
5. Enterprise: require approval via /api/sandbox/approve
6. On approve → swap generation pointers in production
```

---

## Merge resolution & caching

For any UUID in a lineage, the platform reconstructs **effective JSON** by walking **`parent_uuid`** and **deep-merging** from root to leaf. Checkpoints materialise that merged state as a full snapshot row.

**Caching:** merge results are cached server-side with a **short TTL**; any write to the chain clears the relevant cache so you do not see stale merged state.

**Integration surface:** use **REST** (`/api/sandbox/*`, tree, diff, promote) and the published API schema ([OPENAPI.md](OPENAPI.md) · [Swagger](https://agentstack.tech/swagger)). Server-side SDKs or internal modules are not part of the public contract.

---

## A/B Tests

A/B tests use **stable bucketing** (hash-based assignment): `hash(user_uuid + ab_seed) % 100`. The same user always gets the same variant across restarts and horizontal scaling.

Use the **REST** routes under `/api/sandbox/ab-test/*` (create test, metrics, variant, result, promote). See [Swagger UI](https://agentstack.tech/swagger) (tag **Sandbox**) or [OPENAPI.md](OPENAPI.md) for request/response shapes.

---

## Canary Rollout

Canary rollout shifts production traffic gradually via `traffic_weight` (0–100). Steps are defined in `rollout_steps` on the anchor's config.

### Default rollout steps

```json
[
  { "weight": 5,  "pause_minutes": 30 },
  { "weight": 20, "pause_minutes": 60 },
  { "weight": 50, "pause_minutes": 120 },
  { "weight": 100, "pause_minutes": 0 }
]
```

Advance or abort via **`POST /api/sandbox/canary/advance`** and **`POST /api/sandbox/canary/abort`** (see [Swagger](https://agentstack.tech/swagger) for bodies).

---

## Promotion Workflow

Each promotion is recorded as an **audit object** (strategy, checks, approvers, timestamps). Example shape:

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

**Enterprise:** promotion may require explicit `POST /api/sandbox/approve` before traffic is switched.

---

## Targeting Segments

Segments are stored as rules in **`project.data.config.segments[]`** and evaluated when forking or routing.

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

```http
POST /api/sandbox/fork
Content-Type: application/json

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

When `data.config.shadow_writes=true` on an environment anchor, production writes to tracked entities can be mirrored asynchronously into the sandbox as diff-patches. The production response is **not** delayed — mirroring is fire-and-forget.

**Available from Pro plan** (see [Subscription Limits](#subscription-limits)).

```http
POST /api/sandbox/fork
```

```json
{
  "source_project_id": 42,
  "env_name": "staging",
  "env_type": "staging"
}
```

If your stack supports **shadow writes**, the platform can mirror production writes into a **staging** environment as diff patches **asynchronously** (production latency is not increased). Wire this through your **platform integration or SDK** if available; otherwise rely on **REST** and deployment-specific docs.

---

## Protected Field Security

Sandbox environments **do not** expose decrypted secrets. Requests with `?decrypt_protected=true` in a sandbox context are **rejected** (`403 sandbox_secret_access_denied`).

Use server-side hash verification instead:

```http
POST /api/sandbox/protected-check
Content-Type: application/json

{
  "env_uuid": "<anchor_uuid>",
  "field_path": "keys.api.payment_secret",
  "value": "sk_test_my_key"
}
```

```json
{ "match": true }
```

The plaintext is compared on the server; the response only indicates match or no match.

---

## Dashboard UI (product)

The hosted web app includes a **sandbox** area with: environment switcher, generation tree, diff viewer, A/B dashboard, canary controls, promotion flow, and segment builder. Exact layout and labels may change between releases — use **REST** and [OpenAPI](OPENAPI.md) (e.g. [Swagger](https://agentstack.tech/swagger)) as the stable integration surface.

---

## Code Examples

### JavaScript / TypeScript: call the REST API

Use `fetch` or your HTTP client with `Authorization: Bearer <token>` and the endpoints in this guide. Example: list environments, then fork — see [Swagger UI](https://agentstack.tech/swagger) or [openapi.json](https://agentstack.tech/openapi.json) for full schemas ([OPENAPI.md](OPENAPI.md)).

If you use the **official web dashboard**, it may expose client hooks or helpers; those are **not** part of the public API contract.

### Environment context header

Sandbox clients should send:

```http
X-AgentStack-Env: feature/payments-v2
```

The platform resolves this header so downstream requests run in the correct **environment** (generation and type are applied server-side).

Alternatively, store the active env in the user session:

```json
{ "data": { "sandbox": { "active_env": "<env_uuid_or_name>" } } }
```

---

## FAQ

**Q: Does forking copy all production data?**  
A: Fork creates lightweight diff-children. Full state is resolved on demand by merging the ancestor chain, so only changed fields are stored in the sandbox.

**Q: Are protected fields (API keys, secrets) copied to sandboxes?**  
A: No. Protected fields are not copied on fork; resolution reads them from the appropriate ancestor. In sandbox context, `?decrypt_protected=true` is blocked — use `POST /api/sandbox/protected-check`.

**Q: How is A/B user assignment stable?**  
A: Stable bucketing with `hash(user_uuid + ab_seed) % 100`. The `ab_seed` is set at test creation and stays fixed.

**Q: What happens when an environment exceeds its TTL?**  
A: Periodic cleanup archives expired environments (`status=archived`) and cleans up diff-children. Production (`generation=0`) is never removed.

**Q: What does canary `traffic_weight` control?**  
A: It is stored on the environment anchor. Your routing layer (gateway, edge config, or app code) should read the **published** traffic weight for that environment to decide what share of traffic uses the new version.
