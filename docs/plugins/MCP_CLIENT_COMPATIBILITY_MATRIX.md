# MCP Client Compatibility Matrix

**Genetic tag:** `core.mcp.universal_client.gen1`  
**Endpoint:** `https://agentstack.tech/mcp`  
**Execute tool:** `agentstack.execute` (alias `agentstack_execute` on `tools/call`)  
**Catalog:** `GET https://agentstack.tech/mcp/actions`

---

## Quick connect (3 steps)

1. **URL:** `https://agentstack.tech/mcp`
2. **Auth:** API key (`X-API-Key`) or OAuth (see table below)
3. **Chat:** natural language; model calls `agentstack.execute` or GPT Actions `execute_tool`

**Anonymous API key (no signup):**

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{"params": {"name": "My AI Project"}}'
```

Response: top-level `user_api_key` / `session_token` + neutral `bootstrap` metadata (header/field names only). Configure the MCP client once — see [CONTEXT_FOR_AI_MCP.md](CONTEXT_FOR_AI_MCP.md) § Anonymous bootstrap. **Cursor:** prefer Device Code ([MCP_QUICKSTART](https://github.com/agentstacktech/cursor-plugin/blob/main/MCP_QUICKSTART.md)).

## Client matrix

| Client | Transport | Auth | Setup doc | Request shape |
|--------|-----------|------|-----------|---------------|
| **Cursor** | Streamable HTTP JSON-RPC | Device Code → Bearer; or `X-API-Key` | [cursor-plugin MCP quick start](https://github.com/agentstacktech/cursor-plugin/blob/main/MCP_QUICKSTART.md) | `{steps:[{action,params}]}` |
| **Claude Code** | Streamable HTTP | `X-API-Key` or Bearer | [claude-plugin MCP quick start](https://github.com/agentstacktech/claude-plugin/blob/main/MCP_QUICKSTART.md) | JSON-RPC `tools/call` |
| **VS Code** | Extension HTTP | SecretStorage API key | [vscode-plugin MCP quick start](https://github.com/agentstacktech/vscode-plugin/blob/main/MCP_QUICKSTART.md) | JSON-RPC |
| **ChatGPT Custom GPT** | GPT Actions OpenAPI | `X-API-Key` or ecosystem OAuth | [gpt-plugin GPT quick start](https://github.com/agentstacktech/gpt-plugin/blob/main/GPT_QUICKSTART.md) | `{tool, params}` → adapter normalizes |
| **ChatGPT MCP Connector** | Streamable HTTP | Token (= API key) or well-known OAuth | [chatgpt-mcp-connector template](https://github.com/agentstacktech/gpt-plugin/blob/main/templates/chatgpt-mcp-connector.template.json) | JSON-RPC |
| **Gemini CLI** | `--transport http` | `X-API-Key` or `/mcp auth` | [gemini-plugin CLI quick start](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_CLI_QUICKSTART.md) | JSON-RPC |
| **AgentStack npm CLI** (`@agentstack/cli`) | First-party terminal product — SDK REST + MCP JSON-RPC (not an IDE MCP host) | Device Code (`agentstack-cli`) or `AGENTSTACK_API_KEY` | [CLI_QUICKSTART.md](../CLI_QUICKSTART.md) | Curated verbs + `execute --action` |
| **Gemini Spark** | Connected Apps MCP URL | DCR + OAuth | [gemini-plugin Spark quick start](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_SPARK_QUICKSTART.md) | JSON-RPC |
| **Gemini Managed Agents** | `mcp_server` tool in API | Headers / credential refresh | [gemini-plugin Managed Agents](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_MANAGED_AGENTS.md) | Platform SDK |
| **OpenAI API** | Remote MCP tool | Bearer or API key header | [MCP_CHATGPT_GEMINI_GUIDE_RU.md](MCP_CHATGPT_GEMINI_GUIDE_RU.md) §4.3 | JSON-RPC |

---

## OAuth URL decision tree

| Client | Authorization URL | Token URL |
|--------|-------------------|-----------|
| Custom GPT Actions (ecosystem) | `https://agentstack.tech/api/oauth2/authorize` | `https://agentstack.tech/api/oauth2/token` |
| MCP Connector / Gemini Spark (well-known) | `https://agentstack.tech/mcp/.well-known/oauth-authorize` | `https://agentstack.tech/mcp/.well-known/oauth-token` |
| DCR registration | `POST https://agentstack.tech/mcp/.well-known/oauth-register` | — |

**ChatGPT redirect:** `https://chat.openai.com/aip/g-*/oauth/callback`  
**Gemini Spark redirect (document):** `https://gemini.google.com/oauth-redirect`

---

## Discovery endpoints

| Endpoint | Purpose |
|----------|---------|
| `GET /mcp` | Root discovery + `client_hints` + `onboarding` bundle |
| `GET /mcp/manifest` | Registry publish metadata (`core_version`, catalog counts, pillars) |
| `GET /mcp/health` | Liveness + `actions_url` |
| `GET /mcp/actions` | Full action catalog (`catalog_etag`; use `If-None-Match` / `?since_etag=&delta=1`) |
| `GET /mcp/actions/summary` | Lightweight totals — prefer over full catalog for polling |
| `POST /mcp/discover/by_intent?intent=...` | Intent-based action suggestions |
| `GET /mcp/prompts/list` · `GET /mcp/prompts/get` | Named multi-step instruction templates |
| `GET /mcp/recipes` | Curated workflows (`card_game_basic`, `saas_trial_system`, …) |
| `GET /mcp/ai_prompt` | Opt-in narrative (incl. `#anonymous-bootstrap`) |
| `GET /mcp/.well-known/oauth-authorization-server` | OAuth metadata (RFC 8414) |
| `GET /mcp/.well-known/oauth-protected-resource` | Protected resource (RFC 9728) |

---

## Verification

```bash
node provided_plugins/cursor-plugin/scripts/verify-mcp-surface-e2e.mjs
node provided_plugins/scripts/validate-all-plugins.mjs
```

---

## Not supported on MCP path

| Feature | Use instead |
|---------|-------------|
| Per-action `tools/list` explosion | `agentstack.execute` + `GET /mcp/actions` |
| Hard-coded action counts in docs | Live `GET /mcp/actions/summary` |
| Device Code in ChatGPT/Gemini browser | API key or OAuth |
| stdio MCP to prod | Streamable HTTP only for remote |
| Per-action tools in `tools/list` | One tool + catalog (ADR MCP_TOOL_NAMING_CONTRACT) |

## Rate limits

MCP shares platform HTTP rate limits with REST. On **HTTP 429**, honor `Retry-After` when present; otherwise exponential backoff (1s → 2s → 5s). Surface `X-Trace-Id` for support. Do not tight-loop `tools/call` or catalog refresh — use `If-None-Match` on `GET /mcp/actions` when polling.
