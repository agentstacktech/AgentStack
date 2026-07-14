# AgentStack documentation (public mirror)

**English only.** This tree mirrors **integrator- and user-facing** guides published alongside the product. For **REST**, **MCP**, **SDK**, **RAG**, and **plugins**, start here and drill into the sections below.

**Product:** [agentstack.tech](https://agentstack.tech) · **OpenAPI:** [Swagger](https://agentstack.tech/swagger)

---

## Start here

| Audience | Document | ~Time to first call |
|----------|----------|---------------------|
| **Product builders** | [BUILD_YOUR_PRODUCT.md](BUILD_YOUR_PRODUCT.md) | 15 min (hosting tutorial) |
| **Dashboard users** | [USER_FEATURES_GUIDE.md](USER_FEATURES_GUIDE.md) | 5 min (UI) |
| **Integrators & automations** | [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md) | 10 min (MCP curl) |
| **AI agents on large repos** | [genetic-system/README.md](genetic-system/README.md) | 10 min (Navigation OS) |
| **REST overview** | [OPENAPI.md](OPENAPI.md) | 5 min (Swagger) |

---

## What's new (Mar–May 2026)

Themes shipped through **0.4.14**: unified **8DNA** data plane, **resilience / RAG** hardening, **offline-friendly messenger** (delta sync, consistent ordering, background workers where supported), **dual-shell + triple-shell** SPA audiences (`/user/*`, `/dev/*`, `/platform/*`), **Agents Fleet** as first-class project DNA, **project support** on the messenger plane, **SDK media v2** (capture denoise + photo ladder), **Web Push** reliability for DMs, **support search** for staff flows, and **MCP scale** — <!-- stats:total_actions -->463<!-- /stats:total_actions --> catalog actions / <!-- stats:mcp_domains -->40<!-- /stats:mcp_domains --> domains via one `agentstack.execute` entry ([MCP_SCALE.md](MCP_SCALE.md), [capability matrix](MCP_CAPABILITY_MATRIX.md)).

**Narrative (monorepo journal):** see the evolution note linked from the main repo `docs/EVOLUTION_AND_RELEASE_2026-05.md` on GitHub (`master` branch).

---

## Section index

| Section | Contents |
|---------|-----------|
| **[messenger/](messenger/)** | Social API integration, offline behaviour, reliability — [`README.md`](messenger/README.md), [`INTEGRATION_QUICKSTART.md`](messenger/INTEGRATION_QUICKSTART.md), [`SOCIAL_API_REFERENCE.md`](messenger/SOCIAL_API_REFERENCE.md), [`OFFLINE_AND_RELIABILITY.md`](messenger/OFFLINE_AND_RELIABILITY.md) |
| **[storage/](storage/)** | Client-side thumbnails, OPFS, resumable uploads, lightbox — [`README.md`](storage/README.md), [`INTEGRATION_QUICKSTART.md`](storage/INTEGRATION_QUICKSTART.md), [`LIGHTBOX_GUIDE.md`](storage/LIGHTBOX_GUIDE.md) |
| **[hosting/](hosting/)** | Static and SPA sites on `/s/` and `/a/` — [`README.md`](hosting/README.md), [`HOSTING_QUICKSTART.md`](hosting/HOSTING_QUICKSTART.md) |
| **[agents/](agents/)** | Agents Fleet REST + MCP surfaces — [`README.md`](agents/README.md), [`INTEGRATION_QUICKSTART.md`](agents/INTEGRATION_QUICKSTART.md), [`OWNERSHIP_AND_SCOPES.md`](agents/OWNERSHIP_AND_SCOPES.md) |
| **[support/](support/)** | Project support threads, eligibility, AI handoff — [`README.md`](support/README.md), [`INTEGRATION_QUICKSTART.md`](support/INTEGRATION_QUICKSTART.md), [`AI_HANDOFF.md`](support/AI_HANDOFF.md) |
| **[dual-shell/](dual-shell/)** | User vs Developer vs Platform shells — [`README.md`](dual-shell/README.md) |
| **[sdk/](sdk/)** | Typed SDK entry points (`sdk.protocol`, `sdk.platform`, façade modules) — [`README.md`](sdk/README.md), [`MEDIA_V2_DENOISE_AND_COMPRESS.md`](sdk/MEDIA_V2_DENOISE_AND_COMPRESS.md) |
| **[api/](api/)** | Topic guides — [`mcp.md`](api/mcp.md), [`rag.md`](api/rag.md), [`sandbox.md`](api/sandbox.md), [`agents.md`](api/agents.md), [`support.md`](api/support.md), [`messenger.md`](api/messenger.md), [`data-access-api.md`](api/data-access-api.md) |
| **MCP** | [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md) · [MCP_QUICKSTART.md](MCP_QUICKSTART.md) · [MCP_SERVER_CAPABILITIES.md](MCP_SERVER_CAPABILITIES.md) · [MCP_TOOLS.md](MCP_TOOLS.md) · [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md) |
| **RAG** | [RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md) |
| **Sandboxes** | [SANDBOX_PLAYGROUND_GUIDE.md](SANDBOX_PLAYGROUND_GUIDE.md) · [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) |
| **Access** | [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md) · [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md) |
| **Subscriptions** | [subscription/SUBSCRIPTION_TIERS.md](subscription/SUBSCRIPTION_TIERS.md) · [subscription/ANONYMOUS_TIER.md](subscription/ANONYMOUS_TIER.md) |
| **[plugins/](plugins/)** | Cursor, Claude, GPT, VS Code — [plugins/README.md](plugins/README.md) |
| **[examples/](examples/)** | MCP recipes |
| **[architecture/](architecture/)** | [DNA_KEY_VALUE_API.md](architecture/DNA_KEY_VALUE_API.md), [API_CHANNELS.md](architecture/API_CHANNELS.md), [ROBOT_READY_8DNA.md](architecture/ROBOT_READY_8DNA.md) |
| **[integrations/](integrations/)** | Integration Hub — recipes, webhooks |
| **[commerce/](commerce/)** | Shop, wallets, buffs |
| **[auth/](auth/)** | OAuth device flow, API keys |
| **[security/](security/)** | Trust, webhook verification |
| **[tutorials/](tutorials/)** | Golden paths 01–08 |
| **[ai-builder/](ai-builder/)** | UAM manifest |
| **Genetic System** | [genetic-system/README.md](genetic-system/README.md) · [overview](genetic-system/GENETIC_SYSTEM_OVERVIEW.md) · [economics](genetic-system/AI_MODEL_ECONOMICS.md) |
| **Build hub** | [BUILD_YOUR_PRODUCT.md](BUILD_YOUR_PRODUCT.md) · [PARITY_MATRIX.md](PARITY_MATRIX.md) · [JOURNEY_MAP.md](JOURNEY_MAP.md) |
| **Maintainers** | [CONTRIBUTING_DOCS.md](CONTRIBUTING_DOCS.md) · [operations-public/PRE_PUBLISH_CHECKLIST.md](operations-public/PRE_PUBLISH_CHECKLIST.md) |

---

## Plugins (separate repositories)

- [cursor-plugin](https://github.com/agentstacktech/cursor-plugin)
- [claude-plugin](https://github.com/agentstacktech/claude-plugin)
- [gpt-plugin](https://github.com/agentstacktech/gpt-plugin)
- [vscode-plugin](https://github.com/agentstacktech/vscode-plugin)

**Cursor plugin v0.4.14 (gen3):** five-layer bundle (rules, skills, commands, agents, hooks) plus **OAuth 2.1 device code** activation — see [plugins/README.md](plugins/README.md).
