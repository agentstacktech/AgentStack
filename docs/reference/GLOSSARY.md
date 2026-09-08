# Glossary (public docs)

Canonical terms for integrator-facing documentation. Vale enforces these in CI.

| Term | Meaning | Avoid |
|------|---------|-------|
| **AGNT** | Platform agent economy token (AgentNet) | AGC, AgentCoin |
| **agUSD** | Stable vault shares over USDT on L2 | platform-native mint ticker |
| **Unified 8DNA** | Single project data store (`data_projects_8dna` narrative) | `data_projects_project`, `data_projects_user` |
| **Project slice** | Project-level row (`user_id=0` convention) | project table FK chains |
| **Dual-shell** | `/user/*` User Hub, `/dev/*` Developer Hub | `/dashboard/*` (legacy) |
| **Triple-shell** | `/platform/*` operator workspace | extra branches in `/dashboard` |
| **Fabric** | Shared memory + events + one action bus | separate microservices per feature |
| **agentstack.execute** | Single MCP entry for all named actions | per-domain MCP tools |
| **sdk.protocol** | SDK facade for DNA, commands, snapshots | raw fetch to undocumented paths |
| **scheduler.create_task** | Create cron/scheduled task | `scheduler.schedule_task` |
| **Project support channel** | Stable project-scoped support identifier | raw channel-id grammar |
| **Capability task (PTC)** | Comfort task ≤3 actions, Compass + MCP aligned | ad-hoc UI flows |
| **Storefront Studio** | Merchant workspace `?mode=studio` | duplicate catalog editor |

**Version line:** <!-- stats:platform_version -->0.4.18<!-- /stats:platform_version --> (`AGENTSTACK_CORE_VERSION`).
