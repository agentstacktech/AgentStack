# AgentStack MCP — Capability Map for AI

**Purpose:** MCP exposes one tool `agentstack.execute` at `https://agentstack.tech/mcp`. Use this map to choose the right **action** and step sequence for a user request. Full action list: `GET /mcp/actions`.

**Plugin mirror:** Cursor skill `provided_plugins/cursor-plugin/plugins/agentstack/skills/agentstack-backend/SKILL.md` § User request → action — autogen via `node provided_plugins/scripts/sync-mcp-hot-path-skill.mjs`.

**Broader context:** [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) (all channels) · [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) (includes HTTP-only fallback: 8DNA + Protein).

**Session bootstrap (default for agents):** `GET /mcp/ai_prompt?mode=contract` once per session → `POST /mcp/discover/by_intent` when intent is ambiguous → `GET /mcp/actions?schemas=hot` for unfamiliar mutations. Full prompt: `?mode=full` · examples: `/mcp/ai_prompt/examples`.

---

## Request shape (agentstack.execute)

```json
{
  "steps": [
    { "id": "step1", "action": "projects.get_projects", "params": {} },
    { "id": "step2", "action": "projects.get_project", "params": { "project_id": { "from": "step1.result.projects[0].id" } } }
  ],
  "context": { "project_id": 2048 },
  "options": { "stopOnError": true }
}
```

- **Working project (OAuth / user key):** Device-code Bearer usually binds to ecosystem `project_id=1`. Set top-level **`context.project_id`** (or `arguments.context` in `tools/call`) to the tenant project before session-scoped actions (`logic.*`, list/write paths that read `current_user.project_id`). Resource-targeted actions may still pass `params.project_id`.
- **Reference previous step:** In `params`, use `{ "from": "stepId.result.path.to.field" }` (e.g. `"p1.result.project_id"`).
- **Reference context:** Use `{ "from": "context.project_id" }` for request context (project_id, user_id, etc.).
- **Conditional step:** Add `"if": { "equals": [ { "from": "pay.result.status" }, "success" ] }` to run a step only when the condition holds.
- **Project-bound API keys** cannot change `context.project_id` without `cross_project` (or `admin`).

---

## User request → action (and typical steps)

| User request (example) | Domain | action(s) | Notes |
|------------------------|--------|-----------|--------|
| Create a project | Projects | `projects.create_project` (signed-in) or `projects.create_project_anonymous` (no key yet) | **Cursor plugin:** `/agentstack-authorize` or Connect — not anonymous. Anonymous returns `user_api_key` / `session_token` + neutral `bootstrap` metadata once; configure client headers (see § Anonymous bootstrap). Then `p1.result.project_id` in later steps. |
| List my projects | Projects | `projects.get_projects` | params: `{}` or `{ "limit": 50 }`. |
| Get one project / project stats | Projects | `projects.get_project`, `projects.get_stats` | Need `project_id` (literal or from previous step). |
| Read/write project config (8DNA) | Projects | `projects.get_data`, `projects.patch_data` | **Leaf paths only** — never full-blob `data=` writes. |
| Tenant sandbox / promote | Generation | `generation.fork`, `generation.diff_vs_prod`, `generation.gates`, `generation.promote` | Strategy from project `auto_promote_strategy`; not deploy scripts. |
| Give user a 7-day trial | Buffs | `buffs.apply_temporary_effect` | Params: project_id, user_id, effect id/code, duration. |
| List active subscriptions / buffs | Buffs | `buffs.list_active_buffs`, `buffs.get_effective_limits` | project_id, optional user_id. |
| Create payment / check status / refund | Payments | `payments.create`, `payments.get`, `payments.refund` | AgentPay — **not** Stripe SDK. Balance: `payments.get_balance`. |
| Wallets (personal / commerce) | Wallets | `wallets.list`, `wallets.deposit`, `wallets.transfer` | User/commerce balances — not project treasury (`finance.project.*`). |
| Project wallet / treasury | Finance | `finance.project.portfolio`, `finance.project.fund`, `finance.project.contribute` | Project-scoped treasury segments; pair with `commerce.sell.activate` payouts. |
| Publish site / get /s/ URL | Hosting | `hosting.site.quick_start`, `hosting.deploy_files`, `hosting.release.promote` | Buckets + releases on one `project_id` — not Vercel/Netlify. |
| CRM contact / deal pipeline | CRM | `crm.upsert_contact`, `crm.list_contacts`, `crm.create_deal`, `crm.move_deal_stage` | Contact 360: `crm.get_contact_360`; CSV: `crm.import_contacts`. |
| Run project agent / fleet | Agents | `agents.list`, `agents.run`, `agents.create_from_template` | One heavy `agents.run` (`wait=true`) per sync `agentstack.execute` batch. |
| Bot channel / simulate | Bots | `bots.create`, `bots.set_brain`, `bots.simulate`, `bots.go_live` | `bots.simulate` is heavy LLM — one per batch; channels via `bots.attach_channel`. |
| Activate seller / storefront | Commerce | `commerce.sell.activate`, `commerce.storefront.seed_plan`, `commerce.storefront.hosted_publish` | Seller onboarding + hosted vitrine — distinct from marketplace REST (`commerce_rest`). |
| Business head / organ projects | Business | `business.create_composite`, `business.command_snapshot`, `business.list_children` | Multi-project organism — distinct from `generation.*` 8DNA sandbox lineage. |
| Mentor / knowledge KB | Knowledge | `knowledge.kb.ingest`, `knowledge.playground`, `knowledge.config.patch` | Tenant KB + mentor simulate; `knowledge.playground` heavy — one per batch. |
| AgentNet proofs / economy | AgentNet | `agentnet.bnb.proof_bundle_for_run`, `agentnet.genome.verify` | AGNT / agUSD rails — never legacy AGC ticker in new integrations. |
| Compass / guided path | Guidance | `guidance.start_path`, `guidance.complete_step`, `guidance.match_playbook` | Platform Compass playbooks — not docs-nav `docs_nav.*`. |
| Field-level data policy (FAP) | Data access | `data_access.set_policy`, `data_access.get_policy` | Prefer FAP over scattered RBAC `if role` checks in app code. |
| AI Builder manifest | AI Builder | `ai_builder.manifest.get`, `ai_builder.compose.preview` | UAM manifest validate before fleet promote or hosted publish. |
| RAG / semantic search / memory | RAG | `rag.collection_create`, `rag.document_add`, `rag.search`, `rag.memory_add` | Tenant KB vs per-session memory — see `rag.memory_search`. |
| Rules / automations | Logic | `logic.create`, `logic.list`, `logic.execute` | No `rules.*` domain — Logic Engine only. |
| Schedule cron job | Scheduler | `scheduler.create_task`, `scheduler.list_tasks`, `scheduler.cancel_task` | |
| Upload files / quota | Storage | `storage.get_quota`, `storage.list_files` + REST `POST /api/storage/upload` | Binary via REST upload endpoint. |
| Marketplace / auction / exchange | REST (same Core) | `GET /mcp/actions` domain **`commerce_rest`** (path hints only) | Not valid `step.action` — use HTTP `/api/marketplace/*`, `/api/exchange/*`. See [MCP_OVERVIEW.md](../MCP_OVERVIEW.md). |
| Login / register / get profile | Auth | `auth.login`, `auth.register`, `auth.get_profile`, `auth.update_profile` | Session/identity. Device Code via plugin OAuth — not a separate MCP action. |
| Assets / inventory | Assets | `assets.create`, `assets.list` | project_id in params. |
| Analytics / usage / metrics | Analytics | `analytics.get_usage`, `analytics.get_metrics` | set_budget is not in the catalog. |
| API keys (project) | API Keys | `apikeys.list`, `apikeys.create`, `apikeys.delete` | Always set `service_caps` on keys for AI agents. Legacy projects API-key aliases are not in catalog. |
| Webhooks / integration recipes | Integrations / notifications | `integrations.list_recipes`, `integrations.install_recipe`, `notifications.send_push` | Inbound Stripe/callback → Integration Hub — not Checkout SDK. `notifications.send` deprecated — use send_push. |
| RBAC / permissions | RBAC | `rbac.check_permission`, `rbac.assign_role`, `rbac.get_roles` | Prefer FAP `data_access.set_policy` for field-level gates. |
| In-app messenger | Social | `social.chat.post`, `social.chat.history` | Project-scoped chat — not a second WebSocket stack. |
| Support staff inbox | Social / support | `social.support.inbox`, `social.support.history` | Staff plane — user channel uses `social.chat.*`. |

---

## Catalog row hints (`capability_descriptor`)

**GET /mcp/actions** rows include:

- `when_to_use` — curated agent hint (`@mcp_tool(when_to_use=…)` + Tier-A overlay; projection via `resolve_capability_slim`)
- `capability_descriptor` — slim metadata (`source`: overlay | handler | live)
- `related_tools` / `related_prompts` / `organ_id` — from `enrich_mcp_catalog_action`
- `instruction_hint` — short duplicate of `when_to_use` for omnibox / Compass
- `catalog_etag` — top-level body field for conditional refresh (same value as `ETag` response header)

Prefer **`when_to_use`** over raw `summary` when choosing between similar actions in the same domain.

**Conditional catalog (clients):**

| Request | Result |
|---------|--------|
| `GET /mcp/actions?since_etag=<etag>` or `If-None-Match: <etag>` | **304** when unchanged |
| `GET /mcp/actions?since_etag=<stale>&delta=1` | Partial delta when worker revision ring knows prior etag; else full catalog + `delta_fallback: revision_unknown` + header `X-Catalog-Delta` |

Response may include **`X-AgentStack-Cache-Epoch`** (namespace `mcp:actions_catalog`) — echo on subsequent API calls when your client supports cache coherence; bump on `POST /mcp/cache/clear`.

See `docs/adr/MCP_FORWARD_COMPAT_2026.md` · `mcp/catalog_etag.py` · `mcp/catalog_revision.py`.

**GET /mcp/organs** — organism index with `neural_actions` prompt ids per domain.

**Generated index:** `docs/_generated/mcp_agent_instruction_index.json` (`curated_when_to_use_domains`, `social_plane_hints`).

---

## Anonymous bootstrap

Use `projects.create_project_anonymous` when the MCP client has no API key yet (try-it flows, scripted agents). **Cursor plugin users:** prefer Device Code OAuth (plugin **Connect**) — anonymous bootstrap is for key-based clients and REST/MCP without prior auth.

| Response field | Usage |
|----------------|--------|
| `user_api_key` | `X-API-Key` header on follow-up `agentstack.execute` calls |
| `session_token` | `Authorization: Bearer <token>` alternative |
| `project_id` | `context.project_id` or `params.project_id` on scoped actions |
| `user_id` | `context.user_id` when an action requires it |
| `project_api_key` | Postel alias of `user_api_key` (same PAT) |
| `bootstrap` | Neutral metadata only (header/field names, no secrets) |

Credentials are returned **once** at the top level. Configure the MCP client or integration to persist headers (env, `mcp.json`, secret store) — do not rely on model memory. Full narrative: `GET /mcp/ai_prompt#anonymous-bootstrap`.

---

## Multi-instruction surfaces (where agent guidance lives)

| Surface | Use for | Notes |
|---------|---------|--------|
| `GET /mcp/actions` | Per-action `when_to_use`, `instruction_hint`, `capability_descriptor` | Authoritative action ids; `catalog_etag` + delta. **`when_to_use` SoT:** live `@mcp_tool(capability_descriptor=…)` on handlers; catalog table is a **projection** merged with fixture overlays — not a second registry to edit by hand. |
| `GET /mcp/prompts/list` · `get` | Named templates (`agentstack_read_bootstrap`, `agentstack_execute_budget`, …) | Opt-in; not embedded in execute results |
| `GET /mcp/recipes` | Curated multi-step workflows | `POST /mcp/recipes/{id}/validate` for progress |
| `GET /mcp/ai_prompt` | Narrative playbooks + `#anonymous-bootstrap` | Safe for classifiers — no live secrets |
| `POST /mcp/discover/by_intent` | Intent → ranked actions + organ hints | Not docs-nav catalog |
| Plugin skills / `CONTEXT_FOR_AI_MCP` | Hot-path table, domain routing | Autogen into Cursor/GPT via sync scripts |
| [MCP_SYNERGIES_AND_INSTRUCTIONS.md](../MCP_SYNERGIES_AND_INSTRUCTIONS.md) | RU synergies + checklists | Points here for EN SoT |
| [MCP_CLIENT_COMPATIBILITY_MATRIX.md](MCP_CLIENT_COMPATIBILITY_MATRIX.md) | Client auth + transport matrix | Listing / integrator onboarding |

**Anti-pattern:** imperative “save keys forever” prose inside tool **results** — use neutral `bootstrap` metadata only (`core.mcp.self_description.gen1`).

---

## Discovery hierarchy (recommended agent flow)

1. **`GET /mcp/ai_prompt`** — read once per session (playbooks + `#anonymous-bootstrap`).
2. **`GET /mcp/actions/summary`** — live `total_actions` without downloading domains.
3. **`GET /mcp/actions?schemas=hot`** — domain-grouped catalog with `inputSchema` on descriptor pilots (~42 actions).
4. **`POST /mcp/discover/by_intent`** — rank actions + organ hints for a natural-language goal.
5. **`GET /mcp/tools/{name}`** — full `inputSchema` when a single action needs deep params.

Install copy-paste configs: [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md). SDK URL constants: `@agentstack/sdk` → `MCP_GUIDANCE_URLS` / `fetchMcpActionsSummary`.

**Copy-paste execute batches:** `GET /mcp/ai_prompt` field `execute_examples` (recipes `mcp_read_bootstrap`, `mcp_hosting_quickstart`, `mcp_integrations_checkout_crm`, `mcp_agents_run_approve`, `mcp_knowledge_ingest`, `mcp_crm_contact_deal`, `mcp_support_inbox`, `mcp_bots_simulate`, `mcp_generation_diff`). Named playbooks: `GET /mcp/prompts/get?name=agentstack_hosting_storefront` (and `agentstack_knowledge_mentor`, `agentstack_guidance_compass`, `agentstack_project_treasury`). Recipe index: `GET /mcp/recipes` · prompt list: `GET /mcp/prompts/list`.

---

## Full action list

**GET /mcp/actions** returns all available `action` values grouped by domain. Use it to discover exact names (e.g. `buffs.apply_temporary_effect`, `projects.create_project_anonymous`) and short summaries. Action count is authoritative at runtime — not hardcoded in markdown docs.

---

## Related docs

- [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) — legacy multi-tool capability map.
- [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) — index.
- [MCP_OVERVIEW.md](../MCP_OVERVIEW.md) — API endpoints.

**Version:** 0.1 — for agentstack.execute. Update when new domains or actions are added; action list is authoritative at GET /mcp/actions.
