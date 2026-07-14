# AgentNet — integrator guide

AgentNet is AgentStack’s **economy plane**: ledger balances, stable unit **agUSD**, and platform credits **AGNT**. This guide covers integrator-level REST and MCP — not internal vault contracts or genome registry implementation.

> **Naming:** Use **AGNT** (compute/credits) and **agUSD** (stable unit). Do not document or integrate against legacy **AGC** tickers.

---

## Three rails (integrator view)

| Rail | What integrators see | Typical use |
|------|----------------------|-------------|
| **L0 ledger** | Project-scoped batches, balances, checkpoints | In-platform credits, audit, proof bundles |
| **agUSD vault** | NAV, deposit confirm, bridge intents | Stable value, opt-in funding flows |
| **Chain attestation** | PTR rails (Base, BNB, Arbitrum, Solana) | Proof-to-task evidence for grants and Fleet runs |

Treat **vault**, **bridge**, and **genome lineage** as **abstractions** — call the documented REST/MCP surfaces; chain-specific deployment is operator-managed.

---

## REST base

All routes below are prefixed with **`/api/agentnet`**.

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/chain-surface` | Public chain/rail metadata |
| `GET` | `/{project_id}/balance` | Project ledger balance |
| `GET` | `/{project_id}/batches` | Posted batches |
| `POST` | `/{project_id}/batches` | Post double-entry batch (idempotent key) |
| `GET` | `/{project_id}/batches/{batch_id}/proof` | Merkle proof for batch |
| `GET` | `/{project_id}/checkpoints/recent` | Recent checkpoint epochs |
| `POST` | `/{project_id}/compute-credits/quote` | Quote AGNT for compute credits |
| `POST` | `/{project_id}/compute-credits/purchase` | Purchase compute credits |
| `GET` | `/{project_id}/vault/nav` | Vault NAV (ERC-4626 style) |
| `POST` | `/{project_id}/vault/deposit-confirm` | Confirm vault deposit (operator/indexer) |
| `GET` | `/{project_id}/bridge/intents` | List bridge intents |
| `POST` | `/{project_id}/bridge/intents` | Create bridge intent (`in` \| `out`) |
| `POST` | `/{project_id}/proof-to-task` | Build proof-to-task bundle |
| `GET` | `/proof-bundle/{bundle_hash}` | Public read-only proof bundle |
| `GET` | `/ptr/rails` | Enabled PTR rails |

Project-scoped chain endpoints also exist under `/api/projects/{project_id}/agentnet/{chain}/` for Solana, BNB, and Arbitrum narratives.

**OpenAPI:** [Swagger](https://agentstack.tech/swagger) · tag **AgentNet**.

---

## MCP highlights

Category **`agentnet`** — see [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

| Action | Use |
|--------|-----|
| `agentnet.balance` | Read project ledger balance |
| `agentnet.post_batch` | Post ledger batch |
| `agentnet.batch_proof` | Merkle inclusion proof |
| `agentnet.checkpoint.list_recent` | Checkpoint history |
| `agentnet.compute_credits.quote` / `purchase` | AGNT compute credits |
| `agentnet.bridge.create_intent` | Bridge settlement intent |
| `agentnet.vault.nav` / `deposit_confirm` | Vault abstraction |
| `agentnet.proof_to_task` | ERC-8004 style bundle |
| `agentnet.ptr.list_rails` | PTR rail catalog |
| `agentnet.solana.proof_bundle_for_run` | Solana PTR bundle for Fleet run |
| `agentnet.bnb.proof_bundle_for_run` | BNB rail bundle |
| `agentnet.testnet.faucet_mint` | Testnet AGNT mint (rate-limited) |

---

## SDK

```typescript
import { sdk } from "@agentstack/sdk";

// Economy façade — prefer sdk.platform.economy or sdk.protocol.execute
const balance = await sdk.protocol.execute({
  action: "agentnet.balance",
  params: { project_id: 42 },
});
```

See [sdk/PLATFORM_SURFACE.md](../sdk/PLATFORM_SURFACE.md) and [commerce/WALLET_AND_PAYMENTS.md](../commerce/WALLET_AND_PAYMENTS.md).

---

## API keys

Use caps such as `agentnet_post`, `payments`, or `project_admin` depending on the route. See [auth/API_KEYS_AND_SCOPES.md](../auth/API_KEYS_AND_SCOPES.md).

---

## Public grant surfaces

Read-only reviewer APIs: `/api/public/grants/*` — see [grants/PTR_AND_PROOF_LAB.md](../grants/PTR_AND_PROOF_LAB.md).

---

## Honest limits

- Testnet faucets and demo mints are rate-limited.
- On-chain submission is **best-effort** per rail; always persist L0 bundle hash before claiming chain finality.
- Bridge finalize endpoints require operator credentials — not all tenants self-serve chain settlement.

**Next:** [grants/PTR_AND_PROOF_LAB.md](../grants/PTR_AND_PROOF_LAB.md) · [PARITY_MATRIX.md](../PARITY_MATRIX.md)
