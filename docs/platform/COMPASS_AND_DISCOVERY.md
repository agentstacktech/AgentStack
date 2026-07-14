# Platform Compass & Discovery Hub

Two complementary surfaces help builders and agents find **what to do next** on AgentStack: **Compass** (⌘K omnibox) and **Discovery Hub** (capability map).

---

## Platform Compass (⌘K)

**Where:** Developer and User shells — keyboard **⌘K** (Ctrl+K on Windows).

**What it does:**

- Ranks routes, playbooks, and micro-paths (T0–T5 priority bands).
- Deep-links into project hubs (CRM, hosting, storefront, wallet).
- Surfaces **playbooks** (multi-step guided flows) and **capability tasks** (≤3 MCP actions).

Compass replaces the legacy raw command palette in dual-shell mode. Integrators do not call Compass directly — agents use MCP discovery and guidance tools below to mirror the same catalog.

---

## Discovery Hub

**Routes:**

| Shell | Path |
|-------|------|
| Developer | `/dev/discover` |
| User | `/user/discover` |
| Project-scoped | `/dev/projects/:id/discover` |

**Tabs:**

- **Map** — topic clusters (hosting, commerce, agents, support, …).
- **Reference** — former docs-api leaves and REST/MCP pointers.

Sidebar **Discover** (`G D`) is the primary entry in modern shells.

---

## MCP: discovery & guidance

### `discovery.get_platform_surfaces`

Lists canonical platform routes (shell, route kind, topic cluster). Use before deep-linking users or choosing automation targets.

```json
{
  "action": "discovery.get_platform_surfaces",
  "params": { "limit": 40 }
}
```

Returns up to **200** rows; default **80**.

### `guidance.list_capability_tasks`

Lists **Platform Task Capability (PTC)** atoms — comfort tasks with `taskId`, `domain`, and `comfortBudget` (max MCP actions per task, typically **≤3**).

```json
{
  "action": "guidance.list_capability_tasks",
  "params": {}
}
```

Pair with Compass playbooks where `kind=capability` — see [platform/CAPABILITY_TASKS.md](CAPABILITY_TASKS.md).

---

## Integrator workflow

1. Call `discovery.get_platform_surfaces` to enumerate routes for your agent’s allowlist.
2. Call `guidance.list_capability_tasks` to pick atomic tasks (`hosting.publish`, `storage.upload`, …).
3. Execute MCP actions via `agentstack.execute` — [MCP_QUICKSTART.md](../MCP_QUICKSTART.md).

---

## UI vs automation parity

| Surface | Best for |
|---------|----------|
| Compass ⌘K | Human operators, playbook nudges |
| Discovery Hub | Browsing capabilities by domain |
| `discovery.get_platform_surfaces` | Agents building navigation manifests |
| `guidance.list_capability_tasks` | Agents respecting comfort budgets |

**Next:** [CAPABILITY_TASKS.md](CAPABILITY_TASKS.md) · [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md)
