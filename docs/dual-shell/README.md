# Dual-shell & triple-shell SPA

Authenticated AgentStack ships **three chrome contexts** behind one account:

| Shell | Route prefix | Audience |
|-------|----------------|----------|
| **User Hub** | `/app/*` | End users — wallets, messaging, support inbox |
| **Developer Hub** | `/dev/*` | Builders — APIs, logic, sandboxes |
| **Platform** | `/platform/*` | Ecosystem operators — diagnostics, economy hubs |

## Resolver

Navigation picks a shell using **audience + capabilities** — not duplicated accounts.

## Integrators

Your **REST and MCP tokens** are unchanged across shells; only which shortcuts appear in the hosted UI differs.

## Design language

Human-readable design constraints (mobile-first 320 px, tap targets): see the dual-shell design doc on GitHub docs (**DESIGN_LANGUAGE_DUAL_SHELL.md** in the AgentStack repository).

## Related publication article

[Dual-shell audience (Article 18)](https://github.com/agentstacktech/AgentStack/blob/master/docs/publication/ARTICLE_18_DUAL_SHELL_AUDIENCE.md)
