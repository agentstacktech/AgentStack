# Project wallet

The **project wallet** module is the dashboard surface for per-project balances, treasury movements, and commerce funding. Data lives in project DNA wallet rows; REST and MCP expose the same operations.

---

## UI

**Route:** Developer shell → project → **Wallet** (`/dev/projects/:id/wallet`).

Segments cover operating balance, treasury, contributions, and AGNT rail summaries (see [economy/AGENTNET_INTEGRATOR_GUIDE.md](../economy/AGENTNET_INTEGRATOR_GUIDE.md)).

---

## REST base

Prefix: **`/api/projects/{project_id}/wallets`**

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/wallets` | List project wallets |
| `POST` | `/wallets/ensure-default` | Ensure default wallet exists |
| `POST` | `/wallets` | Create wallet |
| `GET` | `/wallets/{wallet_id}` | Get wallet |
| `PUT` | `/wallets/{wallet_id}` | Update wallet metadata |
| `DELETE` | `/wallets/{wallet_id}` | Delete wallet |
| `GET` | `/wallets/{wallet_id}/balance` | Balance snapshot |
| `POST` | `/wallets/{wallet_id}/deposit` | Deposit |
| `POST` | `/wallets/{wallet_id}/withdraw` | Withdraw |
| `POST` | `/wallets/{wallet_id}/transfer` | Transfer between wallets |

Additional transaction list endpoints exist under the same wallet prefix.

---

## MCP (`wallets.*`)

| Action | Purpose |
|--------|---------|
| `wallets.list` | List wallets for project |
| `wallets.create` | Create project wallet |
| `wallets.deposit` | Deposit funds |
| `wallets.transfer` | Same-project transfer |

Payments and marketplace settlement use `payments.*` and `rest.commerce.*` — see [commerce/WALLET_AND_PAYMENTS.md](../commerce/WALLET_AND_PAYMENTS.md).

---

## Finance bundle MCP

| Action | Purpose |
|--------|---------|
| `finance.project.portfolio` | Operating + treasury + AGNT summary |
| `finance.project.fund` | Fund project from ecosystem USD |
| `finance.project.contribute` | Member contribution |
| `finance.project.contributions.list` | Contribution history |

---

## Example: ensure default and deposit

```bash
curl -sS -X POST "https://agentstack.tech/api/projects/42/wallets/ensure-default" \
  -H "Authorization: Bearer $TOKEN"
```

```json
{
  "action": "wallets.deposit",
  "params": {
    "project_id": 42,
    "wallet_id": "WALLET_ID",
    "amount": 100,
    "currency": "USDT"
  }
}
```

---

## API keys

Use `payments`, `project_admin`, or domain-specific caps per route. Wallet reads often accept `payments` or `agentcoin` family caps on finance MCP tools.

**Next:** [economy/AGENTNET_INTEGRATOR_GUIDE.md](../economy/AGENTNET_INTEGRATOR_GUIDE.md) · [commerce/WALLET_AND_PAYMENTS.md](../commerce/WALLET_AND_PAYMENTS.md)
