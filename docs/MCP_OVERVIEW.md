# MCP — Overview and Architecture

- [Overview](#overview)
- [Architecture](#architecture)
- [API Endpoints](#api-endpoints)

**Other sections:** [Quick start](MCP_QUICKSTART.md) · [Tools reference](MCP_TOOLS.md) · [Features and examples](MCP_FEATURES_EXAMPLES.md)

---

## Overview

**MCP (Model Context Protocol)** is a universal server for managing all AgentStack services through a single interface. MCP gives AI agents (Google Studio, Cursor, and others) full access to the platform via a standardized set of tools.

### Main characteristics

- **Protocol version:** 2.0
- **Total tools available:** 62+
- **Streaming support:** Yes
- **Idempotency:** Yes
- **Job tracking:** Yes
- **Port:** 8000 (via API Gateway)

### Key capabilities

- ✅ Full project management (including anonymous creation)
- ✅ Authentication and authorization
- ✅ Payment management
- ✅ Task scheduler
- ✅ Analytics and metrics
- ✅ API key management
- ✅ Rules Engine (server-side logic)
- ✅ Webhooks
- ✅ Notifications
- ✅ Wallets
- ✅ Buff system (temporary and persistent effects)

---

## Architecture

### Project structure

```
mcp/
├── main.py              # FastAPI app, middleware, registration
├── routes.py            # API endpoints for tools
├── tools.py             # Implementation of all MCP tools
├── sdk_wrapper.py       # SDK wrapper for project operations
└── dependencies.py      # Dependencies
```

### Components

1. **FastAPI Application** (`main.py`)
   - CORS middleware
   - Metrics middleware
   - Correlation ID tracking
   - Health checks
   - Prometheus metrics

2. **Routes** (`routes.py`)
   - `GET /mcp/tools` — list all available tools
   - `GET /mcp/discovery` — discovery endpoint for clients
   - `POST /mcp/tools/{tool_name}` — execute tool
   - `POST /mcp/tools/{tool_name}/stream` — streaming execution
   - `GET /mcp/jobs/{job_id}` — job status

3. **Tools Registry** (`tools.py`)
   - All tools registered in `MCP_TOOLS`
   - Pydantic models for request validation
   - Error handling and logging

4. **SDK Wrapper** (`sdk_wrapper.py`)
   - Wrapper over HTTP API for project operations
   - Uses existing endpoints from `agentstack-core`
   - Unified interface for all operations

---

## API Endpoints

**Public endpoints (no X-API-Key):** Calls without the auth header are allowed for `GET /mcp/tools` (tool list) and for the `projects.create_project_anonymous` tool (create anonymous project and get keys). All other requests require `X-API-Key`.

### Discovery and metadata

#### `GET /mcp/tools`
Returns the list of all available tools, grouped by category. **Can be called without X-API-Key.**

**Response:**
```json
{
  "tools": {
    "auth": [...],
    "payments": [...],
    "projects": [...],
    ...
  },
  "version": "2.0",
  "total_tools": 60,
  "description": "Full AgentStack MCP integration..."
}
```

#### `GET /mcp/discovery`
Discovery endpoint for automatic capability detection.

**Response:**
```json
{
  "protocol_version": "2.0",
  "capabilities": {
    "streaming": true,
    "idempotency": true,
    "job_tracking": true
  },
  "services": {
    "auth": {...},
    "projects": {...},
    ...
  }
}
```

### Tool execution

#### `POST /mcp/tools/{tool_name}`
Executes the specified tool.

**Request:**
```json
{
  "tool": "projects.create_project",
  "params": {
    "name": "My Project",
    "description": "Project description"
  },
  "idempotency_key": "optional-key"
}
```

**Response:**
```json
{
  "success": true,
  "data": {...},
  "error": null,
  "trace_id": "uuid"
}
```

#### `POST /mcp/tools/{tool_name}/stream`
Executes the tool with a streaming response (Server-Sent Events).

**Response:** SSE stream with events:
- `status: started`
- `status: processing`
- `status: completed`
- `status: error`

#### `GET /mcp/jobs/{job_id}`
Returns the status of an async job.
