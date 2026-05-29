# Robot-ready 8DNA

Autonomous agents can treat project **data keys** as instruction DNA: a local JSON snapshot plus the MCP action catalog is enough to plan tool loops without scraping the hosted UI.

## Practices

1. Pin `GET /mcp/actions` (or [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md)) beside your snapshot
2. Use stable key paths (`project.data.*`, `user.data.*`) — [DNA_KEY_VALUE_API.md](DNA_KEY_VALUE_API.md)
3. Respect RBAC and service_caps on API keys
4. Prefer `agentstack.execute` batches with `stopOnError` and idempotency keys

**Tutorial:** [../tutorials/08_robot_mcp_loop.md](../tutorials/08_robot_mcp_loop.md)
