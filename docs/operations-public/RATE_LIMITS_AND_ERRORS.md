# Rate limits and errors

| Code | Typical cause |
|------|----------------|
| 401 | Missing or expired auth |
| 403 | Role, service_caps, or FAP deny |
| 429 | Subscription limit or overage disabled |

**MCP:** prefer `options.idempotency_key` for async jobs. Tool errors often return HTTP 200 with `isError` in step results — see [MCP_OVERVIEW.md](../MCP_OVERVIEW.md).

**Plans:** [../subscription/SUBSCRIPTION_TIERS.md](../subscription/SUBSCRIPTION_TIERS.md)
