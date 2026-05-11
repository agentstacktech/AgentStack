# Social / chat MCP actions — reference snapshot

This document summarizes the **`social`** category from the capability matrix. **Authoritative list:** live catalog from **`GET /mcp/actions`** or the full mirror [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

## Highlights

| Prefix | Typical use |
|--------|-------------|
| `social.chat.*` | Delta stream, CRDT updates, channel history |
| `social.admin.*` | Ecosystem-operator diagnostics (privileged caps) |
| `social.support.*` | Project support threads (see [support/](../support/)) |

As of the published matrix snapshot, **`social`** lists **79** actions — largest category in the catalog.

## Usage shape

All MCP calls go through the universal executor your client already uses (`POST /mcp` with `agentstack.execute` semantics — see [api/mcp.md](../api/mcp.md)).

## Subscription / tiers

Some actions require caps (`agents_run`, `agents_admin`, etc.). Missing caps return structured errors — handle like any other RBAC denial.
