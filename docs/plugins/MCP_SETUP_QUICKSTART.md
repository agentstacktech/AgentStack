# MCP setup — install & configure (all clients)

**Genetic tag:** `docs.plugins.setup.gen1`  
**Endpoint:** `https://agentstack.tech/mcp`  
**Execute tool:** `agentstack.execute`  
**Live catalog:** `GET https://agentstack.tech/mcp/actions` (558 public actions · 47 domains — check `/mcp/actions/summary` for current totals)

Copy-paste blocks below. For client-specific quirks see [MCP_CLIENT_COMPATIBILITY_MATRIX.md](MCP_CLIENT_COMPATIBILITY_MATRIX.md).

---

## 1. Verify the server (no auth)

```bash
curl -s https://agentstack.tech/mcp/health
curl -s https://agentstack.tech/mcp/actions/summary
```

---

## 2. Cursor (recommended for developers)

**Install:** [Cursor marketplace — AgentStack](https://marketplace.cursorapi.com/) or local dev: `node provided_plugins/cursor-plugin/scripts/install-local.mjs` → **Developer: Reload Window**.

**Auth (pick one):**

| Method | Steps |
|--------|--------|
| **Connect (OAuth)** | Plugin panel → AgentStack MCP → **Connect** (G-A174) |
| **Device Code** | Chat command `/agentstack-authorize` — writes `~/.cursor/mcp.json` |
| **API key** | [agentstack.tech/me/keys](https://agentstack.tech/me/keys) → add header below |

**Lean `~/.cursor/mcp.json`** (after OAuth or for API key):

```json
{
  "mcpServers": {
    "agentstack": {
      "type": "streamable-http",
      "url": "https://agentstack.tech/mcp",
      "headers": {
        "Content-Type": "application/json",
        "Authorization": "Bearer YOUR_ACCESS_TOKEN"
      }
    }
  }
}
```

API key variant — replace `Authorization` with `"X-API-Key": "ask_..."`.

**More:** [cursor-plugin MCP_QUICKSTART](https://github.com/agentstacktech/cursor-plugin/blob/main/MCP_QUICKSTART.md) · [LOCAL_INSTALL](https://github.com/agentstacktech/cursor-plugin/blob/main/LOCAL_INSTALL.md)

---

## 3. Claude Code

After installing the Claude plugin, run `/agentstack:login` (Device Code), or:

```bash
claude mcp add agentstack --transport http https://agentstack.tech/mcp \
  --header "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  --header "Content-Type: application/json"
```

API key fallback:

```bash
claude mcp add agentstack --transport http https://agentstack.tech/mcp \
  --header "X-API-Key: ask_YOUR_KEY" \
  --header "Content-Type: application/json"
```

Verify: `claude mcp list` then `/mcp` in chat.

**More:** [claude-plugin MCP_QUICKSTART](https://github.com/agentstacktech/claude-plugin/blob/main/MCP_QUICKSTART.md)

---

## 4. VS Code

Install the AgentStack extension from the marketplace, open the command palette → **AgentStack: Sign in** or paste an API key into SecretStorage.

**More:** [vscode-plugin MCP_QUICKSTART](https://github.com/agentstacktech/vscode-plugin/blob/main/MCP_QUICKSTART.md)

---

## 5. ChatGPT & Gemini (browser)

No local `mcp.json` — configure inside the chat product:

| Product | Guide |
|---------|--------|
| ChatGPT Custom GPT / MCP Connector | [MCP_BROWSER_QUICKSTART.md](MCP_BROWSER_QUICKSTART.md) |
| Gemini Spark (Connected Apps) | same + [GEMINI_SPARK_QUICKSTART](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_SPARK_QUICKSTART.md) |
| RU walkthrough | [MCP_BROWSER_QUICKSTART_RU.md](MCP_BROWSER_QUICKSTART_RU.md) |

---

## 6. Anonymous project (try without signup)

For GPT Actions, scripts, or any key-based client **before** you have an account:

```bash
curl -s -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{"params": {"name": "My AI Project"}}'
```

Response includes top-level `user_api_key` / `project_api_key` and neutral `bootstrap` metadata (header names only — no secrets duplicated in prose).

**Configure your client once** with `X-API-Key` (or Bearer where supported). Do not ask the model to “remember” the key — use env, `mcp.json`, or the host’s secret store.

Full narrative: [CONTEXT_FOR_AI_MCP.md](CONTEXT_FOR_AI_MCP.md) § Anonymous bootstrap · `GET /mcp/ai_prompt#anonymous-bootstrap`

**Cursor users:** prefer OAuth (§2) instead of anonymous create.

---

## 7. First authenticated call

```bash
curl -s -X POST https://agentstack.tech/mcp \
  -H "Content-Type: application/json" \
  -H "X-API-Key: ask_YOUR_KEY" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/call",
    "params": {
      "name": "agentstack.execute",
      "arguments": {
        "steps": [{ "id": "1", "action": "discovery.list", "params": {} }]
      }
    },
    "id": 1
  }'
```

---

## 8. Troubleshooting

| Symptom | Fix |
|---------|-----|
| 401 / unauthorized | Re-run Connect or `/agentstack-authorize`; check key at [me/keys](https://agentstack.tech/me/keys) |
| Empty tools list | URL must be `https://agentstack.tech/mcp` (not `/api/...`) |
| `service_cap_denied` | Widen API key caps or use a key with the needed domain |
| Stale action names | `GET /mcp/actions` with `If-None-Match` / `catalog_etag`; run `discovery.list` |
| Cursor plugin missing MCP | Reload Window; click **Connect** on plugin MCP — do not hand-edit plugin `mcp.json` with empty Bearer |

**Deep diagnose:** `/agentstack-diagnose` (Cursor) · `node provided_plugins/cursor-plugin/scripts/verify-mcp-surface-e2e.mjs`

---

## Next

- Overview: [MCP_OVERVIEW.md](../MCP_OVERVIEW.md)
- Lean quickstart: [MCP_QUICKSTART.md](../MCP_QUICKSTART.md)
- AI integrators: [CONTEXT_FOR_AI_MCP.md](CONTEXT_FOR_AI_MCP.md)
- Capability matrix: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md)
