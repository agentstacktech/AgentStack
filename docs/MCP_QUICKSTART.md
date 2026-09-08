# MCP — Quick start

Lean setup for Cursor, Claude Code, VS Code, or any streamable-http MCP client.

## 1. URL

```text
https://agentstack.tech/mcp
```

## 2. Lean `mcp.json` (Cursor / Claude / generic)

```json
{
  "mcpServers": {
    "agentstack": {
      "type": "streamable-http",
      "url": "https://agentstack.tech/mcp",
      "headers": {
        "Content-Type": "application/json"
      }
    }
  }
}
```

- **Cursor:** use plugin **Connect** (OAuth) or `/agentstack-authorize` (Device Code) — do not ship a static `Authorization` in the plugin bundle.
- **API key:** add `"X-API-Key": "ask_..."` to `headers` when not using OAuth.

## 3. Verify

```bash
curl -s https://agentstack.tech/mcp/health | jq .
curl -s https://agentstack.tech/mcp/actions/summary | jq .
```

Optional authenticated probe (from monorepo):

```bash
node provided_plugins/cursor-plugin/scripts/verify-mcp-surface-e2e.mjs
```

## 4. First call

List tools (JSON-RPC):

```bash
curl -s -X POST https://agentstack.tech/mcp \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_KEY" \
  -d '{"jsonrpc":"2.0","method":"tools/list","params":{},"id":1}'
```

Execute one step:

```bash
curl -s -X POST https://agentstack.tech/mcp \
  -H "Content-Type: application/json" \
  -H "X-API-Key: YOUR_KEY" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
      "name": "agentstack.execute",
      "arguments": {
        "steps": [{ "id": "1", "action": "discovery.list", "params": {} }]
      }
    },
    "id": 2
  }'
```

## 5. Anonymous project (no signup)

```bash
curl -s -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{"params": {"name": "My AI Project"}}'
```

Save `user_api_key` from the response for subsequent calls. Configure your client headers once — see [plugins/MCP_SETUP_QUICKSTART.md](plugins/MCP_SETUP_QUICKSTART.md) §6.

## Next

- Full API: [api/mcp.md](api/mcp.md)
- Client matrix: [plugins/MCP_CLIENT_COMPATIBILITY_MATRIX.md](plugins/MCP_CLIENT_COMPATIBILITY_MATRIX.md)
- Overview: [MCP_OVERVIEW.md](MCP_OVERVIEW.md)
