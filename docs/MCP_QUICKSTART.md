# MCP — Quick start

Connect AI assistants and automation to AgentStack with **one executor** that dispatches **named actions** from the live catalog.

## 1. Get the action list

```http
GET https://agentstack.tech/mcp/actions
Authorization: Bearer <token>
```

Snapshot for offline reading: [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md) (**226** actions in the May 2026 export).

## 2. Execute an action

Use your integration’s universal MCP entry (`POST /mcp` with `agentstack.execute` semantics — see [api/mcp.md](api/mcp.md)).

## 3. Cursor plugin — OAuth 2.1 Device Code

The Cursor plugin ships an **`/agentstack-init`** flow:

1. Run the login command from the plugin (see plugin README on GitHub **cursor-plugin**).
2. Paste the short code at **`https://agentstack.tech/activate`** (device authorization page).
3. The plugin writes a scoped Bearer token to your local MCP config.

Tokens are narrow — **`service_caps`** derive from OAuth scopes.

### Refresh / revoke

Use the plugin’s documented refresh helpers; clearing MCP cache may call **`POST /mcp/cache/clear`** when rotating discovery.

## Next

- [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md) — full ecosystem index.
- [plugins/README.md](plugins/README.md) — Cursor / Claude / GPT / VS Code bundles.
