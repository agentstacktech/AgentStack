# PTR & Proof Lab

**Proof-to-task rails (PTR)** attach verifiable evidence to Agent Fleet runs and grant narratives. Public surfaces let reviewers inspect bundles without tenant credentials.

---

## Public web routes

| Route | Purpose |
|-------|---------|
| [/grants](https://agentstack.tech/grants) | Grant program overview |
| [/grants/demo](https://agentstack.tech/grants/demo) | Interactive proof lab |
| [/grants/demo?chain=solana](https://agentstack.tech/grants/demo?chain=solana) | Solana rail demo |
| [/grants/demo?chain=base](https://agentstack.tech/grants/demo?chain=base) | Base rail narrative |
| [/grants/demo?chain=bnb](https://agentstack.tech/grants/demo?chain=bnb) | BNB / BSC rail narrative |

---

## Evidence rails (integrator view)

| Rail | MCP status | Proof bundle action |
|------|------------|---------------------|
| **Solana** | `agentnet.solana.status` | `agentnet.solana.proof_bundle_for_run` |
| **BNB (BSC)** | `agentnet.bnb.status` | `agentnet.bnb.proof_bundle_for_run` |
| **Arbitrum** | `agentnet.arb.status` | `agentnet.arb.proof_bundle_for_run` |
| **Base** | `agentnet.chain.rails` | `agentnet.proof_to_task` |

List all enabled rails:

```json
{
  "action": "agentnet.ptr.list_rails",
  "params": {}
}
```

REST: `GET /api/agentnet/ptr/rails`

---

## Public read APIs

| Endpoint | Purpose |
|----------|---------|
| `GET /api/public/grants/evidence-snapshot` | Aggregated demo evidence (rate-limited) |
| `GET /api/public/grants/chain-surface` | Chain metadata for reviewers |
| `GET /api/agentnet/proof-bundle/{bundle_hash}` | Read-only bundle by SHA-256 hash |

---

## Build a bundle for a Fleet run

```json
{
  "action": "agentnet.solana.proof_bundle_for_run",
  "params": {
    "project_id": 42,
    "run_id": "RUN_UUID"
  }
}
```

Optional on-chain steps: `agentnet.solana.submit_validation` (best-effort; devnet).

---

## Testnet plane

| Action | Purpose |
|--------|---------|
| `agentnet.testnet.list_profiles` | Chain profiles + faucet policy |
| `agentnet.testnet.run_scenario` | Grant demo / bridge smoke scenarios |
| `agentnet.testnet.faucet_mint` | Rate-limited AGNT mint |

---

## Economy context

Ledger batches and checkpoints underpin bundles — [economy/AGENTNET_INTEGRATOR_GUIDE.md](../economy/AGENTNET_INTEGRATOR_GUIDE.md).

**Synergy:** [agent-proof-anchor.md](../explanation/synergies/agent-proof-anchor.md)

**Next:** [agents/INTEGRATION_QUICKSTART.md](../agents/INTEGRATION_QUICKSTART.md) · [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md) § agentnet
