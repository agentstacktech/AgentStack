# Access and field policy (summary)

**Canonical document:** [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md) — full format, ecosystem masking, triggers, and examples.

AgentStack access control has three layers:

1. **API keys (`service_caps`)** — restrict which services an automation key may call.
2. **RBAC** — roles and permissions for people on a project.
3. **Field Access Policy (FAP)** — which JSON fields each role may read, mask, or hide.

For REST DataAccess endpoints and MCP `data_access.*` actions, start with [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md) and [api/data-access-api.md](api/data-access-api.md).

**Readable overview (L1–L3 narrative):** the introduction sections in [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md#access-control-layers).
