# REST · MCP · SDK parity

| Domain | REST (examples) | MCP prefix | SDK façade |
|--------|-----------------|------------|------------|
| CRM | `/api/projects/{id}/crm/*` | `crm.*` | `sdk.platform.api` |
| AgentNet | `/api/agentnet/*` | `agentnet.*`, `agentnet.solana.*`, `agentnet.testnet.*` | `sdk.platform.economy` |
| Discovery | `/api/discovery/*` | `discovery.*`, `guidance.*` | Compass UI + `sdk.platform.api` |
| Storefront | `/api/commerce/storefront/*` | `commerce.storefront.*` | `sdk.commerce` |
| SEO | `/api/seo/*` | `seo.*` | `sdk.platform.api` |
| Projects | `GET /api/projects` | `projects.*` | `sdk.platform.api` |
| 8DNA | `GET/POST /api/dna/data` | `commands.*`, DNA tools | `sdk.protocol`, `sdk.platform.dna` |
| Messenger | `/api/social/*` | `social.*` | `sdk.messenger`, `sdk.social` |
| Support | `/api/support/*` | `social.support.*` | `sdk.support` |
| Agents | `/api/projects/{id}/agents/*` | `agents.*` | `sdk.agents` |
| RAG | `/api/rag/*` | `rag.*` | REST via `sdk.platform.api` |
| Storage | `/api/storage/*` | `storage.*` | `sdk.storage` |
| Hosting | `/api/hosting/*` | `hosting.*` | `sdk.hosting` |
| Integrations | webhooks + hub API | `integrations.*` | `sdk.integrations` |
| Commerce | `/api/commerce/*` | `commerce_rest.*`, `assets.*` | `sdk.commerce` |
| Logic | `/api/logic/*`, commands | `logic.*` | `sdk.logic`, `sdk.protocol` |
| Scheduler | scheduler routes | `scheduler.*` | `sdk.scheduler` |
| Buffs | buff endpoints | `buffs.*` | `sdk.buffs` |
| AI Builder | manifest endpoints | `ai_builder.*` | UAM validators in SDK |

**Authoritative MCP list:** [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md)
