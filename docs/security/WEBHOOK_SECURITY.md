# Webhook security

## Inbound (to AgentStack)

1. Read raw body before JSON parse
2. Compute HMAC with your signing secret
3. Compare to header using constant-time equality
4. Reject replays with idempotency keys where supported

## Outbound (from AgentStack)

Integration Hub records delivery attempts; use MCP `integrations.list_issues` and `integrations.replay_delivery` for failures.

**Next:** [../integrations/INTEGRATION_QUICKSTART.md](../integrations/INTEGRATION_QUICKSTART.md)
