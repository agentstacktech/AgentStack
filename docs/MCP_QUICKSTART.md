# MCP — Quick start and setup

- [Quick start](#quick-start)
- [Setup in Cursor](#setup-in-cursor)
- [Debugging and examples](#connection-debugging)

**Other sections:** [Overview and architecture](MCP_OVERVIEW.md) · [Tools reference](MCP_TOOLS.md) · [Features and examples](MCP_FEATURES_EXAMPLES.md)

---

## Quick start

MCP is available in the cloud at **[agentstack.tech](https://agentstack.tech)** — no local server setup required.

1. **Get an API key:** Sign up at [agentstack.tech](https://agentstack.tech) and create a project in the dashboard, or create an anonymous project with a single request (see below).
2. **Configure the plugin** (Cursor, Claude, VS Code, or GPT): Set MCP URL to `https://agentstack.tech/mcp` and your API key. 60+ actions are available in chat.

**Check availability:**
```bash
# List available tools (no key required)
curl https://agentstack.tech/mcp/tools
```

---

## Setup in Cursor

### Method 1: HTTP MCP Server (recommended)

Cursor supports MCP servers over HTTP. Configure as follows:

#### Step 1: Create the config file

Create `cursor-mcp-config.json` in your home directory or in Cursor settings:

**Windows:**
```
%APPDATA%\Cursor\User\globalStorage\mcp-config.json
```

**macOS:**
```
~/Library/Application Support/Cursor/User/globalStorage/mcp-config.json
```

**Linux:**
```
~/.config/Cursor/User/globalStorage/mcp-config.json
```

#### Step 2: Add MCP server configuration

```json
{
  "mcpServers": {
    "agentstack": {
      "type": "streamable-http",
      "url": "https://agentstack.tech/mcp",
      "headers": {
        "Content-Type": "application/json",
        "X-API-Key": ""
      },
      "tools": {
        "enabled": true,
        "autoDiscover": true
      }
    }
  }
}
```

Put your API key in `X-API-Key` (see [Getting an API key](#getting-an-api-key) to obtain one).

#### Step 3: Restart Cursor

After adding the configuration, restart Cursor for changes to take effect.

### Method 2: Via Cursor UI settings

1. Open Cursor
2. Go to **Settings** → **Features** → **MCP Servers**
3. Click **Add Server**
4. Fill in the form:
   - **Name**: `agentstack`
   - **Type**: `Streamable HTTP`
   - **URL**: `https://agentstack.tech/mcp`
   - **Headers**: `Content-Type: application/json`, `X-API-Key`: your API key (if required)
5. Save settings

### Method 3: Using from Chat (no config)

If the MCP server is running, you can use it directly via the HTTP API in Cursor chat:

```
Create a project via MCP:
POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous
Body: {"tool": "projects.create_project_anonymous", "params": {"name": "Test Project"}}
```

### Getting an API key

If an API key is required for authentication:

1. **Create an anonymous project** (get API key):
```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "My Project"
    }
  }'
```

2. **Use the returned `api_key`** in Cursor configuration

### Using in Cursor Chat

After setup you can use MCP tools directly in Cursor chat:

#### Example 1: Create a project

```
Create a new project via MCP named "My Test Project"
```

Cursor will automatically call `projects.create_project_anonymous` with the parameters.

#### Example 2: List projects

```
Show all my projects via MCP
```

#### Example 3: User management

```
Add user user@example.com to project 1025 with role member
```

**Note:** `add_user` and `remove_user` require a Professional subscription.

### Connection debugging

If the MCP server is not working in Cursor:

1. **Check MCP availability:**
```bash
curl https://agentstack.tech/mcp/tools
```

2. **Check Cursor logs:**
   - Open Developer Tools (Ctrl+Shift+I / Cmd+Option+I)
   - Go to Console
   - Look for MCP connection errors

3. **Check configuration:**
   - Ensure `url` = `https://agentstack.tech/mcp` and `type` = `streamable-http`
   - Ensure the API key in `X-API-Key` is correct (no extra spaces)

### Example commands for Cursor

#### Project creation and setup

```
Create an anonymous project "My AI Project" via MCP and show the API key
```

#### Working with users

```
Show all users of project 1025 via MCP
```

#### API key management

```
Create a new API key for project 1025 named "Development Key"
```

#### Getting stats

```
Show stats for project 1025 via MCP
```

#### Viewing activity

```
Show the last 10 activity events for project 1025
```

### Alternative: Using an HTTP client

If MCP setup in Cursor does not work, you can use an HTTP client directly in code:

```python
import httpx

async def create_project_via_mcp(name: str):
    async with httpx.AsyncClient() as client:
        response = await client.post(
            "https://agentstack.tech/mcp/tools/projects.create_project_anonymous",
            json={
                "tool": "projects.create_project_anonymous",
                "params": {"name": name}
            }
        )
        return response.json()
```

MCP is available at **https://agentstack.tech/mcp**. No extra environment variables are required.

### Practical Cursor scenarios

#### Scenario 1: Create a test project

**Request in Cursor:**
```
Create a new test project via MCP named "Test Project" and describe what you got
```

**What happens:**
1. Cursor calls `projects.create_project_anonymous`
2. Gets `project_id`, `api_key`, `auth_key`
3. Shows the result in chat

**Cursor response:**
```
✅ Project created successfully!

Project ID: 1025
API Key: ask_abc123xyz...
Auth Key: ask_abc123xyz...

You can now use this API key to work with the project.
```

#### Scenario 2: Attach project to a user

**Request in Cursor:**
```
Attach project 1025 to user with email user@example.com. Use auth_key: ask_abc123xyz...
```

**What happens:**
1. Cursor calls `projects.attach_to_user`
2. Project ownership is transferred to the user
3. A new API key is created for the owner

#### Scenario 3: Get project info

**Request in Cursor:**
```
Show info for project 1025: stats, users, settings
```

**What happens:**
1. Cursor calls several tools:
   - `projects.get_project` — main info
   - `projects.get_stats` — stats
   - `projects.get_users` — user list
   - `projects.get_settings` — settings
2. Aggregates results and shows them in a readable form

#### Scenario 4: API key management

**Request in Cursor:**
```
Create a new API key for project 1025 named "Production Key" and show it
```

**What happens:**
1. Cursor calls `projects.create_api_key`
2. Gets the new key
3. Shows it (⚠️ save it — it is only shown once)

#### Scenario 5: View activity

**Request in Cursor:**
```
Show the last 20 activity events for project 1025
```

**What happens:**
1. Cursor calls `projects.get_activity` with `limit=20`
2. Shows the list of events with timestamps

### Usage tips

1. **Always save API keys** — they are only shown once when created
2. **Use anonymous creation** for quick testing
3. **Attach projects** to real users for production
4. **Check subscription** before adding users (Professional required)
5. **Use trace_id** when debugging issues

### Troubleshooting

#### Issue: "Tool not found"

**Solution:**
- Ensure the MCP server is running
- Check that the tool name is correct (with dot: `projects.create_project`)
- Check Cursor configuration

#### Issue: "Connection refused"

**Solution:**
- Check that the server is running on port 8000
- Check firewall settings
- Ensure `url` in config is correct (`https://agentstack.tech/mcp`)

#### Issue: "403 Forbidden" when adding a user

**Solution:**
- This is expected — Professional subscription is required
- Upgrade the user's subscription or use another method

#### Issue: "Invalid auth_key" when attaching

**Solution:**
- Use the correct `auth_key` from the anonymous creation response
- Check that the project is not already attached to another user
