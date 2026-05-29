# OAuth 2.1 device code (Cursor plugin)

1. Install [cursor-plugin](https://github.com/agentstacktech/cursor-plugin)
2. Run activation from the plugin — browser opens device authorization
3. Approve scopes; plugin stores narrow tokens
4. MCP calls use Bearer token on `POST https://agentstack.tech/mcp`

**Related:** [../plugins/README.md](../plugins/README.md) · [../MCP_QUICKSTART.md](../MCP_QUICKSTART.md)
