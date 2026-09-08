# AgentStack in the browser — ChatGPT & Gemini

**Audience:** manage AgentStack projects from ChatGPT or Gemini chat windows.  
**Endpoint:** `https://agentstack.tech/mcp`  
**Setup hub:** [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md)

---

## Step 0 — get an API key (one time)

Terminal or any HTTP client:

```bash
curl -s -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{"params": {"name": "My AI Project"}}'
```

The response returns `user_api_key` or `project_api_key` at the **top level**, plus neutral `bootstrap` metadata (which headers to set — no duplicated secrets in instructions).

**Configure once** in ChatGPT/Gemini settings or your password manager — paste the key into the connector’s API Key / Token field (`X-API-Key` header). Do not rely on the model to store it.

Signed-in users: create a scoped key at [agentstack.tech/me/keys](https://agentstack.tech/me/keys) instead.

---

## ChatGPT in the browser

### Option A — Custom GPT (~10 minutes, easiest)

Works with ChatGPT Plus and above.

1. Open [chatgpt.com](https://chatgpt.com) → **Explore GPTs** → **Create**.
2. **Configure** → **Actions** → import OpenAPI from the repo:  
   `https://github.com/agentstacktech/gpt-plugin/blob/main/openapi/agentstack-mcp.yaml`
3. **Authentication** → **API Key**:
   - Header: `X-API-Key`
   - Value: your key from step 0
4. **Instructions** — copy from [GPT_INSTRUCTIONS.md](https://github.com/agentstacktech/gpt-plugin/blob/main/GPT_INSTRUCTIONS.md).
5. **Save** → “Only me” or “Anyone with link”.

Open your GPT and use natural-language commands (see cookbook below).

---

### Option B — MCP Connector (Developer Mode)

Requires ChatGPT Plus / Pro / Business with Developer Mode.

1. [chatgpt.com](https://chatgpt.com) → **Settings** → **Apps & Connectors** → **Advanced** → enable **Developer mode**.
2. **Create** connector:
   - **Name:** AgentStack
   - **URL:** `https://agentstack.tech/mcp`
   - **Authentication:** Token → paste your API key (AgentStack also accepts OAuth)
3. You should see **1 tool**: `agentstack.execute`.

In chat: **+** next to the input → **More** → select AgentStack.

Template JSON: [chatgpt-mcp-connector.template.json](https://github.com/agentstacktech/gpt-plugin/blob/main/templates/chatgpt-mcp-connector.template.json)

---

## Gemini in the browser

### Connected Apps (Gemini Spark only)

> **Google limitation:** custom MCP in web Gemini requires **Gemini Spark** (US, 18+, personal Google account, **Keep Activity** on). Standard Gemini chat does not support custom MCP.

1. [gemini.google.com](https://gemini.google.com) → **Settings & help** → **Connected Apps**.
2. Enable **Keep Activity** under Settings → Activity.
3. **Custom apps for Spark** → **Add a custom app**.
4. **MCP server URL:** `https://agentstack.tech/mcp`
5. Complete OAuth if prompted, or use API key credentials from step 0.
6. **Next** → confirm.

In Spark chat: `@` → your custom app → command.

**No Spark?** Use ChatGPT (option A) or [Gemini CLI](https://github.com/agentstacktech/gemini-plugin/blob/main/GEMINI_CLI_QUICKSTART.md) in the terminal.

---

## Chat commands (copy-paste)

| Goal | Example prompt |
|------|----------------|
| List projects | “Call AgentStack discovery.list and show my projects.” |
| Publish a page | “Use hosting.publish_static on project_id X with this HTML…” |
| Check catalog | “GET /mcp/actions/summary and tell me how many actions exist.” |
| Read-only bootstrap | “Run recipe mcp_read_bootstrap with continueOnError.” |

Full cookbook: [MCP_CHAT_COMMAND_COOKBOOK.md](MCP_CHAT_COMMAND_COOKBOOK.md)

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| 401 / unauthorized | Re-check API key in GPT/Gemini connector settings |
| Gemini missing custom app | Requires Gemini Spark (US); otherwise use ChatGPT |
| ChatGPT “Tool scan failed” | Retry save 2–3×; URL must be exactly `https://agentstack.tech/mcp` |
| Model invents data | Say: “Call AgentStack API — do not fabricate project IDs.” |
| Permission denied | Key has narrow `service_caps` — create a wider key at [me/keys](https://agentstack.tech/me/keys) |

Live probe (no local Core clone):

```bash
curl -sS -H "X-API-Key: $AGENTSTACK_API_KEY" https://agentstack.tech/mcp/actions | head
```

Or use your IDE plugin **Connect** + `/agentstack-diagnose`.

---

## What to choose

| | ChatGPT Custom GPT | ChatGPT MCP Connector | Gemini Spark |
|--|-------------------|----------------------|--------------|
| Difficulty | ★★☆ | ★★★ | ★★★ |
| Plus / Spark required | Plus | Plus | Spark |
| Works in normal browser chat | ✅ | ✅ | Spark only |
| One-time setup | ✅ | ✅ | ✅ |

**Recommendation:** start with **ChatGPT Custom GPT (option A)** for the fastest path.

**RU guide:** [MCP_BROWSER_QUICKSTART_RU.md](MCP_BROWSER_QUICKSTART_RU.md) · **Matrix:** [MCP_CLIENT_COMPATIBILITY_MATRIX.md](MCP_CLIENT_COMPATIBILITY_MATRIX.md)
