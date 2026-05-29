# Trust and security

- **Transport:** HTTPS to agentstack.tech for all API and MCP traffic
- **Authentication:** OAuth, session cookies (hosted UI), API keys, MCP Bearer tokens
- **Authorization:** RBAC + optional field access policy + key service caps
- **Webhooks:** Verify HMAC signatures on inbound; sign outbound deliveries — [WEBHOOK_SECURITY.md](WEBHOOK_SECURITY.md)
- **Secrets:** Store connection secrets in project integration settings, not in client bundles

Operator-only diagnostics are not part of this public mirror.
