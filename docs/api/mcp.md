# MCP API — single-tool model

**Endpoint:** `https://agentstack.tech/mcp`  
**Tool:** `agentstack.execute` (alias `agentstack_execute` on `tools/call`)  
**Catalog:** `GET /mcp/actions` (`catalog_etag` in JSON; `ETag` / `If-None-Match` / `?since_etag=` → 304; `?delta=1` for partial refresh)

MCP is the primary integration channel for AI agents. One JSON-RPC tool runs batched **steps**; each step uses a canonical **action** id from the live catalog.

## Authentication

| Method | Header |
|--------|--------|
| API key | `X-API-Key: <key>` |
| OAuth / Device Code | `Authorization: Bearer <token>` |

Optional tenant scope: `X-Project-ID: <id>` (not ecosystem `1` for tenant work).

**Anonymous bootstrap (no key):** JSON-RPC `tools/call` with action `projects.create_project_anonymous` in the first step, or REST shim `POST /mcp/tools/projects.create_project_anonymous`.

Response includes top-level `user_api_key`, `session_token`, `project_id`, `user_id`, and neutral `bootstrap` metadata (field names only — no imperative AI blocks). See [CONTEXT_FOR_AI_MCP.md](../plugins/CONTEXT_FOR_AI_MCP.md) § Anonymous bootstrap.

## Execute shape

```http
POST https://agentstack.tech/mcp
Content-Type: application/json
X-API-Key: your_key

{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "agentstack.execute",
    "arguments": {
      "steps": [
        { "id": "s1", "action": "discovery.list", "params": {} }
      ],
      "options": { "stopOnError": true }
    }
  },
  "id": 1
}
```

Direct REST batch (same semantics): `POST /mcp` with body `{ "steps": [...], "options": {...} }`.

GPT Actions use `{ "tool": "<action>", "params": {} }` — normalized to steps server-side.

## Discovery

| Endpoint | Purpose |
|----------|---------|
| `GET /mcp` | Root pointers + onboarding metadata |
| `GET /mcp/actions` | Full action catalog (`outputSchema` per action when declared); body includes `catalog_etag` |
| `GET /mcp/actions?since_etag=<etag>` | 304 when catalog unchanged (header `ETag` or query param) |
| `GET /mcp/actions?since_etag=<stale>&delta=1` | Partial delta when prior revision known; else full catalog + `delta_fallback: revision_unknown` |
| `GET /mcp/actions/summary` | Count only |
| `GET /mcp/discovery` | Protocol + execute tool schema |
| `GET /mcp/health` | Health + `protocol_versions_supported`, `manifest_url` |
| `GET /mcp/manifest` | Registry publish manifest |
| `POST /mcp/discover/by_intent?intent=...` | Intent matches + `confidence`, `example_params` |
| `GET /mcp/prompts/get?name=...` | Onboarding prompts (`agentstack_execute_budget`, `agentstack_write_modes`, …) |

## Response envelope

`tools/call` returns MCP `content` (JSON text) plus optional `structuredContent` when the step result is a dict. Errors include `recovery_hint` codes (`auth_required`, `service_cap_denied`, `action_not_found`, …).

## Write modes & budget

- **Sync batch deadline:** 60s total; at most one heavy LLM action per sync execute — see prompt `agentstack_execute_budget`.
- **8DNA writes:** `projects.patch_data` with `write_mode` — see prompt `agentstack_write_modes` and `WRITE_MODES.md`.

## Client matrix

See [MCP_CLIENT_COMPATIBILITY_MATRIX.md](../plugins/MCP_CLIENT_COMPATIBILITY_MATRIX.md) for Cursor, Claude, VS Code, GPT, Gemini, and CLI setup.

## Related

- [MCP_QUICKSTART.md](../MCP_QUICKSTART.md) — lean `mcp.json`
- [architecture/API_CHANNELS.md](../architecture/API_CHANNELS.md) — MCP vs REST vs Protein
- [plugins/README.md](../plugins/README.md) — plugin entry for integrators
