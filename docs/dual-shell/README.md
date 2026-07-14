# Dual-shell & triple-shell SPA

Authenticated AgentStack ships **three chrome contexts** behind one account:

| Shell | Route prefix | Audience |
|-------|----------------|----------|
| **User Hub** | `/user/*` | End users — wallets, messaging, project workspaces |
| **Developer Hub** | `/dev/*` | Builders — APIs, logic, sandboxes, support staff tools |
| **Platform** | `/platform/*` | Ecosystem operators — diagnostics, economy hubs |
| **Hosted mini-app** | `/app/dev/:id`, `/app/prod/:id` | AI Builder / hosted app shells (not the main dashboard chrome) |

## Resolver

Navigation picks a shell using **audience + capabilities** — not duplicated accounts.

## Integrators

Your **REST and MCP tokens** are unchanged across shells; only which shortcuts appear in the hosted UI differs.

## Compass & Discovery (0.4.14)

- **Platform Compass** (⌘K / Ctrl+K) ranks routes, playbooks, and capability tasks across User and Developer hubs.
- **Discovery Hub** (`/user/discover`, `/dev/discover`) replaces the old dev-docs accordion with a capability map.
- See [platform/COMPASS_AND_DISCOVERY.md](../platform/COMPASS_AND_DISCOVERY.md).

## CRM & project hubs

Project-scoped modules (CRM, wallet, commerce studio) live under `/user/projects/:id/*` and `/dev/projects/:id/*` — not legacy `/dashboard/*` paths.

## Design language

Human-readable design constraints (mobile-first 320 px, tap targets): see the dual-shell design doc on GitHub docs (**DESIGN_LANGUAGE_DUAL_SHELL.md** in the AgentStack repository).

## Related publication article

[Dual-shell audience (Article 18)](https://github.com/agentstacktech/AgentStack/blob/master/docs/publication/ARTICLE_18_DUAL_SHELL_AUDIENCE.md)
