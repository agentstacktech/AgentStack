# MCP scale glossary

**Live catalog:** `GET https://agentstack.tech/mcp/actions`  
**Offline snapshot:** [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md)

| Term | Meaning (current platform line) |
|------|----------------------------------|
| **MCP tool surface** | One registered tool: `agentstack.execute` (batched steps). |
| **Catalog actions** | Named `action` values inside steps (e.g. `projects.get_project`, `rag.search`). |
| **Tool domains** | Grouping labels in the action catalog (projects, social, rag, integrations, …). |
| **MCP tools (discovery)** | Count returned by discovery endpoints; may differ slightly from catalog rows during rollout. |

**Snapshot (platform <!-- stats:platform_version -->0.4.14<!-- /stats:platform_version -->):** <!-- stats:total_actions -->463<!-- /stats:total_actions --> catalog actions · <!-- stats:mcp_domains -->40<!-- /stats:mcp_domains --> domains · see matrix header for regeneration date.

**Do not confuse with:** legacy blog copy saying «70+ actions» — that referred to an early catalog slice. Always use the matrix or live `/mcp/actions` for integrations.

**Related:** [MCP_QUICKSTART.md](MCP_QUICKSTART.md) · [MCP_TOOLS.md](MCP_TOOLS.md) · [plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md)
