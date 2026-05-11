# Agents — integration quickstart

## REST (scoped)

```http
GET /api/projects/{projectId}/agents
Authorization: Bearer …
```

```http
GET /api/users/me/agents
Authorization: Bearer …
```

Exact shapes are in **OpenAPI** ([OPENAPI.md](../OPENAPI.md)).

## MCP

Use **`agents.run`**, **`agents.list`**, **`agents.get`** via your MCP client (`POST /mcp` / `agentstack.execute`). Required caps are listed per row in [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

```json
{
  "tool": "agentstack.execute",
  "arguments": {
    "action": "agents.run",
    "payload": { "agent_uuid": "…", "input": {} }
  }
}
```

(Exact envelope matches your SDK version — prefer **`@agentstack/sdk`** typed helpers when available.)
