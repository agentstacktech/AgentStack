# MCP — Implementation details and examples

- [Implementation details](#implementation-details)
- [Usage examples](#usage-examples)
- [Summary statistics](#summary-statistics)

**Other sections:** [Quick start](MCP_QUICKSTART.md) · [Overview](MCP_OVERVIEW.md) · [Tools reference](MCP_TOOLS.md)

---

## Implementation details

### 1. Anonymous project creation

**Problem:** AI agents (Google Studio, Cursor) cannot create projects without prior user registration.

**Solution:** `projects.create_project_anonymous`

- ✅ No authentication required
- ✅ **Automatically creates an anonymous user** with `is_anonymous: true`
- ✅ Generates API key with prefix `anon_ask_` (ANONYMOUS tier)
- ✅ Returns `user_api_key`, `project_api_key`, `session_token`, `user_id`
- ✅ User is immediately authenticated and ready to use
- ✅ Lets you start working with the project right away

**⚠️ ANONYMOUS tier limits:**
- 1 project max (cannot create more)
- 1 API key (cannot create more)
- 1,000 Logic Engine calls/month (vs 10,000 on FREE)
- 20 MB JSON storage (vs 100 MB on FREE)
- 20 triggers (vs 50 on FREE)
- Payments disabled

**Conversion:**
- Use `auth.convert_anonymous_user` to convert to FREE tier
- After conversion all limits increase to FREE tier

**Usage:**
```json
{
  "tool": "projects.create_project_anonymous",
  "params": {
    "name": "Test Project",
    "description": "Created by AI agent"
  }
}
```

### 2. Attaching projects to users

**Problem:** Anonymous projects need to be linked to a real user.

**Solution:** `projects.attach_to_user`

- ✅ Requires authorization (new owner's user_id)
- ✅ Validates `auth_key` from anonymous creation
- ✅ Transfer ownership:
  - Updates `project.user_id`
  - Creates records in `data_projects_user`
  - Removes old anonymous key
  - Creates new key for the owner

**Usage:**
```json
{
  "tool": "projects.attach_to_user",
  "params": {
    "project_id": 1025,
    "auth_key": "ask_...",
    "user_id": 123
  }
}
```

### 3. Subscription limits

**Project user management** (`add_user`, `remove_user`) requires a **Professional subscription**.

**Implementation:**
- Check at API endpoint level (`agentstack-core/endpoints/projects_endpoints.py`)
- Uses `SubscriptionService.get_user_subscription()`
- Checks `plan_type` in ['pro', 'professional', 'enterprise']
- Returns 403 Forbidden when subscription is missing
- Error is passed through MCP unchanged

**Error:**
```json
{
  "success": false,
  "error": "HTTP 403: Professional subscription required for adding/removing project users. Please upgrade your subscription."
}
```

### 4. SDK Wrapper

**Architecture:**
- `ProjectsSDKWrapper` — wrapper over HTTP API
- Uses `shared.clients.http.request` for requests
- Unified interface for all operations
- Automatic header building (Authorization, X-Project-ID)
- Uses existing endpoints (no duplicated logic)

**Details:**
- API keys managed via `/api/apikeys/keys` (no duplicates)
- Settings read from project `config`
- Activity from `/api/projects/{id}/logs`

### 5. Error handling

**Strategy:**
- SDK/API errors are passed through as-is
- HTTP status codes are not converted
- `trace_id` kept for debugging
- Uses `httpx.HTTPStatusError` for HTTP errors

**Error format:**
```json
{
  "success": false,
  "error": "HTTP 403: Insufficient permissions",
  "trace_id": "uuid"
}
```

### 6. Request validation

**Pydantic models:**
- All new project methods use Pydantic models
- Automatic parameter validation
- Type-safe interface

**Example:**
```python
class CreateProjectRequest(BaseModel):
    name: str
    description: Optional[str] = None
    config: Optional[Dict[str, Any]] = None
    ...
```

### 7. Demo mode

**Support:**
- Check `demo_context.is_readonly()`
- Block write operations in read-only mode
- Check capabilities via `DemoCapabilities`

---

## Usage examples

### Example 1: Anonymous project creation

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project_anonymous \
  -H "Content-Type: application/json" \
  -d '{
    "tool": "projects.create_project_anonymous",
    "params": {
      "name": "My AI Project",
      "description": "Created by AI agent"
    }
  }'
```

**Response:**
```json
{
  "success": true,
  "data": {
    "project_id": 1025,
    "api_key": "ask_abc123...",
    "auth_key": "ask_abc123...",
    "project": {
      "id": 1025,
      "name": "My AI Project",
      ...
    }
  },
  "trace_id": "uuid"
}
```

### Example 2: Attach project to user

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.attach_to_user \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.attach_to_user",
    "params": {
      "project_id": 1025,
      "auth_key": "ask_abc123...",
      "user_id": 123
    }
  }'
```

**Response:**
```json
{
  "success": true,
  "data": {
    "success": true,
    "project_id": 1025,
    "new_api_key": "ask_xyz789...",
    "message": "Project 1025 successfully attached to user 123"
  },
  "trace_id": "uuid"
}
```

### Example 3: Get project list

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.get_projects \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.get_projects",
    "params": {
      "is_active": true,
      "limit": 10
    }
  }'
```

### Example 4: Add user (requires Professional)

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.add_user \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.add_user",
    "params": {
      "project_id": 1025,
      "email": "user@example.com",
      "role": "member"
    }
  }'
```

**When subscription is missing:**
```json
{
  "success": false,
  "error": "HTTP 403: Professional subscription required for adding/removing project users. Please upgrade your subscription.",
  "trace_id": "uuid"
}
```

### Example 5: Streaming execution

```bash
curl -X POST https://agentstack.tech/mcp/tools/projects.create_project/stream \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "tool": "projects.create_project",
    "params": {
      "name": "Streaming Project"
    }
  }'
```

**Response (SSE):**
```
data: {"status": "started", "trace_id": "uuid"}

data: {"status": "processing", "progress": 50}

data: {"status": "completed", "result": {...}}
```

---

## Summary statistics

### By category

- **Auth**: 4 tools
- **Projects**: 8 tools
- **Logic Engine**: 9 tools
- **Processors**: 3 tools
- **Commands**: 2 tools
- **Payments**: 4 tools
- **Scheduler**: 11 tools ⭐ (largest category)
- **Analytics**: 2 tools
- **API Keys**: 3 tools
- **Buffs**: 10 tools
- **Assets**: 4 tools

**Total: 1 tool (agentstack.execute), 60+ actions** — action list: GET /mcp/actions

### Recent updates

1. ✅ Anonymous project creation
2. ✅ Attach projects to users
3. ✅ Full project management methods
4. ✅ Integration with existing endpoints
5. ✅ Subscription check for user management

---

## Conclusion

The MCP server provides **full access** to all AgentStack functionality through a single interface. Focus areas:

- **AI agent convenience** (anonymous creation)
- **Flexibility** (project attachment)
- **Security** (subscription checks, validation)
- **Reliability** (error handling, trace_id)
- **Performance** (streaming, caching)

All tools use existing endpoints, ensuring consistency and no code duplication.

---

**Document version:** 1.0  
**Last updated:** 2025-01-29  
**Author:** AgentStack Development Team
