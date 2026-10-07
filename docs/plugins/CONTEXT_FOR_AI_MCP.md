# AgentStack MCP — Capability Map for AI

**Purpose:** MCP exposes one tool `agentstack.execute` at `https://agentstack.tech/mcp`. Use this map to choose the right **action** and step sequence for a user request. Full action list: `GET /mcp/actions`.

**Plugin mirror:** Cursor skill `provided_plugins/cursor-plugin/plugins/agentstack/skills/agentstack-backend/SKILL.md` § User request → action — autogen via `node provided_plugins/scripts/sync-mcp-hot-path-skill.mjs`.

**Closed-loop spine:** autogen via `node provided_plugins/scripts/sync-mcp-hot-path-skill.mjs` from `mcp_agent_instruction_index.json`.

**Broader context:** [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) (all channels) · [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) (includes HTTP-only fallback: 8DNA + Protein). Operator access is membership plus `context.project_id` (session table below). Docs navigation is separate from `GET /mcp/actions`.

**Session bootstrap (default for agents):** `GET /mcp/prompts/get?name=agentstack_session_setup` **first** (auth → project → `context.project_id`) → `GET /mcp/ai_prompt?mode=contract` → `POST /mcp/discover/by_intent` when intent is ambiguous → `GET /mcp/actions?schemas=hot` for unfamiliar mutations. Full prompt: `?mode=full` · examples: `/mcp/ai_prompt/examples`.

**Discovery hierarchy (SoT: `instruction_plane.get_onboarding_bundle()`):**

| Layer | Entry | When |
|-------|-------|------|
| MCP resources | `agentstack://instructions/session`, `agentstack://instructions/work-graph`, `agentstack://planes`, `agentstack://catalog/summary` | resources/list clients |
| HTTP bootstrap | `http_bootstrap_ladder()` — contract → manifest → organs → `schemas=public` | Before first execute batch |
| MCP ladder | `machine_discovery_ladder` — session → **work_next** → discovery.status → search → describe → preflight | After `context.project_id` |
| Single action | `discovery.describe` (`related_surfaces` cross-links) | Before unfamiliar mutation |

### Multi-instruction surfaces

| Surface | URI / action | Role |
|---------|--------------|------|
| Session ladder | MCP resource `agentstack://instructions/session` · prompt `agentstack_session_setup` | Auth → project → bind context |
| Work graph | MCP resource `agentstack://instructions/work-graph` · prompt `agentstack_closed_loop_autonomy` | Default spine after project bind |
| Planes atlas | MCP resource `agentstack://planes` | Domain prefix → first action |
| Catalog totals | MCP resource `agentstack://catalog/summary` · `discovery.summary` | Etag + counts without full catalog |
| Compass | **`guidance.list`** → `guidance.match_playbook` → `guidance.start_path` | PlaybookIds before NL match |

### Mandatory session order

| Phase | Goal | Typical actions |
|-------|------|-----------------|
| 1 Authenticate | MCP identity | Plugin OAuth (preferred), `auth.login`, or `projects.create_project_anonymous` |
| 2 Project | Workspace scope | `projects.get_projects` → pick id; or `projects.create_project` |
| 3 Bind context | Tenant isolation | `context.project_id` on every batch (OAuth often defaults to ecosystem `1`) |
| 3b Access model | Mutations allowed? | **User-scoped OAuth/PAT/bearer_token:** membership RBAC — set `context.project_id`; **`auth.get_profile.mutation_allowed`**. **Project-scoped API key JWT:** `auth.switch_project` + Bearer. |
| **4 Work Graph route** | **Route → plan → verify (default for multi-step)** | Prompt **`agentstack_closed_loop_autonomy`** · recipe **`mcp_work_loop_v1`** · `agents.work_next` → follow `packet.next_action` — **before** domain CRUD or `discovery.search` shopping |
| 5 Domain work | Feature actions | Recipe `mcp_session_setup` then `mcp_read_bootstrap` (includes optional `hosting.project.status` for primary `/s/` URL) or **`agentstack_safe_project_cycle`** (tenant mutations) |
| 6 Safe cycle (tenant) | Sandbox → promote | Preflight → domain mutate → verify → `generation.diff_vs_prod` → `generation.gates` → `generation.promote` — prompt `agentstack_safe_project_cycle` |

### Work Graph routing (after `context.project_id` is bound)

| User intent | First tool | Never first |
|-------------|-----------|-------------|
| Do project work / fleet / plan graph | `agents.work_next` then **`agentstack_closed_loop_autonomy`** | `discovery.search` catalog shopping |
| What tools exist? | `discovery.list` | N/A |
| How does one action work? | `discovery.describe` | `agents.plan_execute` |
| Status-only poll | `agents.work_status` | Re-running `discovery.search` |

**Autonomous loop API keys:** preset cap alias **`agents_autonomous_loop`** — scoped keys for headless `mcp_work_loop_v1` / `agentstack_closed_loop_autonomy` (includes `agents.work_next`, `plan_claim`, `plan_execute`; denies catalog mutations via competence guard).

<!-- BEGIN:AUTOGEN-CLOSED-LOOP-SPINE -->
**Closed loop (one paragraph):** After `context.project_id` is bound, multi-step goals use **discovery routed_goal** (not keyword-only ranking), **plan graph v2** with CAS revisions, **`agents.work_next` → `agents.ensure_agent` (provision) → `agents.plan_claim` → `agents.plan_execute`**, **instruction_packet** skills, then **agents.run** / **agents.orchestrate** with evidence and completion gates. Blocked leaves: read `diagnostics.harness_state` on `work_next` packets. **Business self-service:** head project + organ children (`business.*`) — same loop on each child `context.project_id`; prompts `agentstack_business_organism` + `agentstack_closed_loop_autonomy`. **Specialized agents:** `agents.team.create` routing + templates + parallel plan nodes. External MCP clients: follow `inverse_orchestration_ladder` (allowed_actions only).
<!-- END:AUTOGEN-CLOSED-LOOP-SPINE -->

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

- **Working project (OAuth / user key / SPA password):** Device-code / Connect / password Bearer is usually **user-scoped** (`bearer_token`; JWT may show ecosystem `project_id=1`). Set top-level **`context.project_id`** to the tenant workspace; **`auth_surface.session_ready`** / **`mutation_allowed`** become true when membership allows — **no** `auth.switch_project` per hop. REST SPA uses the same rule: one Bearer + **`X-Project-ID`**.
- **Project-bound API keys** pin `project_id` in the token — use **`auth.switch_project`** + update Bearer when changing tenant, or keep `context.project_id` aligned with JWT.
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
| Tenant sandbox / promote | Generation | **Prompt:** `agentstack_tenant_8dna_supply` · `generation.fork`, `generation.diff_vs_prod`, `generation.gates`, `generation.promote`, `generation.realign_to_prod` | Safe cycle default for paid tenants; strategy from `auto_promote_strategy`; not deploy scripts. |
| Give user a 7-day trial | Buffs | `buffs.apply_temporary_effect` | Params: project_id, user_id, effect id/code, duration. |
| List active subscriptions / buffs | Buffs | `buffs.list_active_buffs`, `buffs.get_effective_limits` | project_id, optional user_id. |
| Grant platform tier (ecosystem op) | Buffs | `buffs.grant_subscription` | Prompt `agentstack_platform_subscription_grant` · PID=1 · personal premium/vip or project launch/business/scale. |
| Grant tenant subscription (support/comp) | Buffs | `buffs.grant_tenant_subscription` | Prompt `agentstack_tenant_buff_grant` · recipe `mcp_tenant_manual_entitlement_grant` · tenant PID>1, project write. |
| Grant tenant inventory (digital goods) | Commerce | `commerce.participant.grant_holding` | Pair with subscription grant when SKU is inventory-backed. |
| Sell tenant subscription (paid checkout) | Commerce + Buffs | `buffs.create_buff` → `commerce.sell.activate` → `payments.create` | Auto-grant via webhook (`recipient_project_id`) or C24 — not manual `apply_buff`. Prompt `agentstack_tenant_subscription_monetization`. |
| Create payment / check status / refund | Payments | `payments.create`, `payments.get`, `payments.refund` | AgentPay — **not** Stripe SDK. Balance: `payments.get_balance`. |
| Wallets (personal / commerce) | Wallets | `wallets.list`, `wallets.deposit`, `wallets.transfer` | User/commerce balances — not project treasury (`finance.project.*`). |
| Project wallet / treasury | Finance | `finance.project.portfolio`, `finance.project.fund`, `finance.project.contribute` | Project-scoped treasury segments; pair with `commerce.sell.activate` payouts. |
| Publish site / get /s/ URL | Hosting | `hosting.site.quick_start`, `hosting.deploy_files`, `hosting.release.promote` | Buckets + releases on one `project_id` — not Vercel/Netlify. **Greenfield** → recipe `mcp_hosting_quickstart`. |
| Edit existing /s/ site (rollback path) | Hosting | `hosting.release.snapshot`, `hosting.deploy_files`, `hosting.release.promote` | Recipe **`mcp_hosting_edit_site_safe`** · prompt `agentstack_hosting_edit_site` — **not** quickstart. **One CSS/HTML line** → recipe `mcp_hosting_edit_text_file_v1` (`hosting.bucket.file.patch` `search_replace` only — **no** prior `file.get`; server refreshes `.gz` + `cache_bust`). |
| TypeScript / SDK / OpenAPI | SDK + MCP | `GET /mcp/actions`, `sdk.protocol`, `getCapabilityMatrix()` | Operator scripts: `@agentstack/sdk`. Hosted page: import map `/sdk/v…` + cookie `/s/{pid}/mcp`. Contract: `/openapi-mcp.json` + `/openapi.json` — not raw fetch sprawl. |
| CRM contact / deal pipeline | CRM | `crm.upsert_contact`, `crm.list_contacts`, `crm.create_deal`, `crm.move_deal_stage` | Contact 360: `crm.get_contact_360`; CSV: `crm.import_contacts`. |
| Run project agent / fleet | Agents | `agents.list`, `agents.run`, `agents.create_from_template` | One heavy `agents.run` (`wait=true`) per sync `agentstack.execute` batch. |
| Closed-loop autonomy (plan + verify + teams) | Agents / Discovery / Projects | `discovery.search`, `agents.plan_get`, `agents.work_next`, `agents.ensure_agent`, `agents.plan_claim`, `agents.plan_execute`, `agents.plan_propose`, `agents.plan_apply_proposal`, `agents.plan_recovery_scan`, `agents.orchestrate`, `agents.team.create` | Prompt **`agentstack_closed_loop_autonomy`** · ladder `closed_loop_ladder`. Spine: **`work_next` → `ensure_agent` (provision) → `plan_claim` → `plan_execute`**. Thread same **`env_uuid`** on sandbox forks. Blocked: `diagnostics.harness_state` + `recovery.next_actions` (not `missing_roles`). Plan patch requires `if_match_revision`. Team goals: prefer routed `agents.team.create` over `integrations.*`. |
| Project AI orchestrator (copilot / support / bot brain) | Agents / Projects | `agents.orchestrate`, `projects.orchestrator.get`, `projects.orchestrator.patch` | **Single SoT:** DNA leaf config.project_orchestrator.agent_uuid. Workspace → channel=workspace; messenger support → channel=messenger + conversation_id=psup_p{pid}_u{uid}; bot → channel=bot + bot_uuid. Memory thread via `rag.memory_*` on memory_session_id from run input. Recipes: `mcp_project_operator_session`, `mcp_orchestrator_memory_bootstrap`, `mcp_project_faq_bootstrap`. Link: **closed loop** prompt above for multi-step copilot goals. |
| Import orchestrator marketplace pack | Projects / Assets | `projects.orchestrator.import_from_asset`, preset `project_orchestrator_pack_v2` | Post-deal fulfillment or `orchestrator.importPack` post-create action — not manual DNA blob replace. |
| Bot channel / simulate | Bots | `bots.create`, `bots.set_brain`, `bots.simulate`, `bots.go_live` | `bots.simulate` is heavy LLM — one per batch; channels via `bots.attach_channel`. Lifecycle: `bots.get` returns `lifecycle` (`draft` \| `active` \| `paused` \| `archived`) and `cleanup_action` (`bots.archive` when retiring). **Canonical retire:** `bots.archive` — not ad-hoc deletes. |
| Activate seller / storefront | Commerce | `commerce.sell.activate`, `commerce.storefront.seed_plan`, `commerce.storefront.hosted_publish` | Seller onboarding + hosted vitrine — distinct from marketplace REST (`commerce_rest`). |
| Business head / organ projects | Business | `business.create_composite`, `business.command_snapshot`, `business.list_children` | Multi-project organism — distinct from `generation.*` 8DNA sandbox lineage. **Self-service:** each organ project runs the same closed loop (route/plan/execute/verify) with `context.project_id` = child id. Prompt: `agentstack_business_organism` + `agentstack_closed_loop_autonomy`. |
| Mentor / knowledge KB | Knowledge | `knowledge.kb.ingest`, `knowledge.playground`, `knowledge.config.patch` | Tenant KB + mentor simulate; `knowledge.playground` heavy — one per batch. **Index heal:** `knowledge.kb.heal_index` — not deprecated alias `knowledge.reindex`. Runbook: MCP prompts `agentstack_knowledge_*` + `/agentstack-safe-cycle`. |
| AgentNet proofs / economy | AgentNet | `agentnet.bnb.proof_bundle_for_run`, `agentnet.genome.verify` | AGNT / agUSD rails — never legacy AGC ticker in new integrations. |
| Compass / guided path | Guidance | `guidance.start_path`, `guidance.complete_step`, `guidance.match_playbook` | Platform Compass playbooks — not docs-nav `docs_nav.*`. |
| Field-level data policy (FAP) | Data access | `data_access.set_policy`, `data_access.get_policy` | Prefer FAP over scattered RBAC `if role` checks in app code. |
| AI Builder manifest | AI Builder | `ai_builder.manifest.get`, `ai_builder.compose.preview` | UAM manifest validate before fleet promote or hosted publish. |
| RAG / semantic search / memory | RAG | `rag.collection_create`, `rag.document_add`, `rag.search`, `rag.memory_add` | Tenant KB vs per-session memory — see `rag.memory_search`. |
| Rules / automations | Logic | `logic.create`, `logic.list`, `logic.execute` | No `rules.*` domain — Logic Engine only. |
| Schedule cron job | Scheduler | `scheduler.create_task`, `scheduler.list_tasks`, `scheduler.cancel_task` | |
| Upload files / quota | Storage | `storage.get_quota`, `storage.list_files` + REST `POST /api/storage/upload` | Binary via REST upload endpoint. |
| Marketplace / auction / exchange | REST (same Core) | `GET /mcp/actions` domain **`commerce_rest`** (path hints only) | Not valid `step.action` — use HTTP `/api/marketplace/*`, `/api/exchange/*`. See [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md). |
| Login / register / get profile | Auth | `auth.login`, `auth.register`, `auth.get_profile`, `auth.update_profile` | Session probe: `auth.get_profile` accepts aliases **`auth.me`**, **`auth.profile`**, `auth.status`, `auth.whoami` (same as REST `GET /api/auth/me`). Health: `discovery.health` → `system.ping`. Catalog: `discovery.list_actions` → `discovery.list`. Device Code via plugin OAuth — not a separate MCP action. |
| Assets / inventory | Assets | `assets.create`, `assets.list` | project_id in params. |
| Analytics / dashboard KPIs | Analytics | `analytics.project_snapshot` | Unified snapshot (activity, finance, CRM, product events). Use `include=` for optional slices. Legacy: `analytics.get_usage` (activity slice only), `analytics.get_metrics` (custom DNA counters `data.metrics[]` — not performance KPIs). set_budget is not in the catalog. |
| API keys (project) | API Keys | `apikeys.list`, `apikeys.create`, `apikeys.delete` | Always set `service_caps` on keys for AI agents. Legacy projects API-key aliases are not in catalog. |
| Webhooks / integration recipes | Integrations / notifications | `integrations.list_recipes`, `integrations.connector_schema`, `integrations.install_recipe`, `integrations.test_hook` | Recipe `mcp_integrations_universal_connect_v1`. Missing connection → **`connection_not_found`**; missing scenario → **`scenario_not_found`** (`list_scenarios`). Legacy `webhooks.*` MCP removed. |
| RBAC / permissions | RBAC | `rbac.check_permission`, `rbac.assign_role`, `rbac.get_roles` | Prefer FAP `data_access.set_policy` for field-level gates. |
| In-app messenger | Social | `social.chat.post`, `social.chat.history` | Project-scoped chat — not a second WebSocket stack. |
| Support staff inbox | Social / support | `social.support.inbox`, `social.support.history` | Staff plane — user channel uses `social.chat.*`. |
| Transactional email (Mail Hub) | Messaging | `messaging.send_email`, `messaging.get_config` | Ecosystem email templates — skill `agentstack-messaging`. Admin ops (`messaging.send_test_email`, etc.) are operator-only. |
| Hosted vertical workspace | Vertical workspace | `vertical_workspace.bootstrap`, `checklist.get` | EDITFLOW / Key2Unity — skill `agentstack-hosted-vertical`; `POST /mcp` + flagship `X-Project-ID`. |
| Tenant safe cycle | Generation | `generation.diff_vs_prod`, `generation.gates`, `generation.promote` | Command `/agentstack-safe-cycle` · prompt `agentstack_safe_project_cycle` — sandbox before prod DNA. |

---

## Employee role → MCP recipe (agent employee readiness)

**Gene:** `core.mcp.employee_role_atlas.gen1`. Recipe ids in the table are the public contract.

| Employee role | `recipe_id` | E2E path (Wave D) |
|---------------|-------------|-------------------|
| Sales / CRM rep | `mcp_crm_contact_deal` | `crm_pipeline` |
| Support staff | `mcp_support_respond_v1` | `support_staff_loop` |
| Project operator | `mcp_project_operator_session` | `project_operator_loop` |
| Fleet agent operator | `mcp_agents_run_approve` | `fleet_operate` |
| DevOps / platform ops | `mcp_universal_safe_change` | `dna_safe_patch` |
| Integration engineer | `mcp_integration_ops_v1` | `integration_discovery` |
| Analyst / researcher | `mcp_analyst_readonly_v1` | — |
| Content writer | `mcp_content_writer_v1` | `hosting_readiness` |
| Storefront / commerce manager | `mcp_hosting_quickstart` | `hosting_readiness` |
| Knowledge / training mentor | `mcp_knowledge_playground` | — (ingest: `mcp_knowledge_ingest`) |
| Support bot operator | `mcp_bots_simulate` | `fleet_read` (`bots.get` + simulate; go_live separate) |
| Business composite operator | `mcp_business_composite_v1` | — (create via Compass `setup-business-head`) |

Start with `GET /mcp/prompts/get?name=agentstack_session_setup`, then `discovery.recipes` or execute the role `recipe_id` as a guided batch.

---

## Product archetype → MCP recipe

**Gene:** `repo.plugins.product_flow.gen1` · **SoT:** `docs/_generated/mcp_agent_instruction_index.json` → `product_archetypes` (do not hand-edit this table).

| Archetype | Default `mcp_recipe_id` |
|-----------|-------------------------|
| static_site | mcp_hosting_quickstart |
| hosted_site_edit | mcp_hosting_edit_site_safe |
| saas | saas_trial_system |
| ecommerce | mcp_hosting_quickstart |
| game | card_game_basic |
| bot_channel | mcp_bots_simulate |
| backend_api | mcp_session_setup |
| project_setup | mcp_read_bootstrap |
| migrate_legacy | mcp_session_setup |

Narrative: [PRODUCT_BUILD_FLOW.md](PRODUCT_BUILD_FLOW.md)

---

## Catalog row hints (`capability_descriptor`)

**GET /mcp/actions** rows include:

- `when_to_use` — curated agent hint (`@mcp_tool(when_to_use=…)` + Tier-A overlay; projection via `resolve_capability_slim`)
- `capability_descriptor` — slim metadata (`source`: overlay | handler | live)
- `related_tools` / `related_prompts` / `organ_id` — from `enrich_mcp_catalog_action`
- `aliases` — accepted synonyms (`auth.me` → `auth.get_profile`, etc.) from `mcp.action_aliases`
- `instruction_hint` — short duplicate of `when_to_use` for omnibox / Compass
- `effect` — machine hint (`kind`, `budget`, `generation`, `write_mode`, `destructive`) from `derive_action_effect()` — read this to predict step impact before calling
- `catalog_etag` — top-level body field for conditional refresh (same value as `ETag` response header)

Prefer **`when_to_use`** + **`effect.kind`** over raw `summary` when choosing between similar actions in the same domain.

**Unauthenticated MCP (no prior session):** `system.ping`, `auth.login`, `auth.register`, `projects.create_project_anonymous`, `discovery.list` (cached catalog). Session probe `auth.get_profile` / `auth.me` returns `unauthorized` until Bearer or X-API-Key is set. Async job poll (`discovery.job_status`, `GET /mcp/jobs/{id}`) requires the **same user** who enqueued the batch.

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

Canonical order is `instruction_plane.http_bootstrap_ladder()` then `machine_discovery_ladder()` (`agents.work_next` before `discovery.status`). The list below is an expanded reference, not a second ladder.

0. **`GET /mcp/prompts/get?name=agentstack_session_setup`** — mandatory session ladder (auth → project → `context.project_id`). Recipe: `mcp_session_setup`.
1. **`GET /mcp/ai_prompt?mode=contract`** — slim contract + `execute_examples` (not full catalog).
2. **`GET /mcp/actions/summary`** — live `total_actions` without downloading domains.
3. **`GET /mcp/actions?schemas=hot`** — domain-grouped catalog with `inputSchema` + `effect` on descriptor pilots.
4. **`POST /mcp/discover/by_intent`** — rank actions + `instruction_slice` (incl. `effect`) for a natural-language goal.
5. **`GET /mcp/prompts/get?name=agentstack_read_bootstrap`** — named playbook when you need a copy-paste batch.
6. **`GET /mcp/recipes`** — multi-step workflows (`options.recipe_id` on execute).
7. **`GET /mcp/tools/{name}`** — full `inputSchema` when a single action needs deep params.

Install copy-paste configs: [MCP_SETUP_QUICKSTART.md](MCP_SETUP_QUICKSTART.md). SDK URL constants: `@agentstack/sdk` → `MCP_GUIDANCE_URLS` / `fetchMcpActionsSummary`.

**Copy-paste execute batches:** `GET /mcp/ai_prompt` field `execute_examples` (recipes `mcp_read_bootstrap`, `mcp_hosting_quickstart`, **`mcp_hosting_edit_site_safe`**, `mcp_integrations_checkout_crm`, `mcp_agents_run_approve`, `mcp_knowledge_ingest`, `mcp_crm_contact_deal`, `mcp_support_inbox`, `mcp_bots_simulate`, `mcp_generation_diff`). Named playbooks: `GET /mcp/prompts/get?name=agentstack_hosting_storefront` · **`agentstack_hosting_edit_site`** (and `agentstack_knowledge_mentor`, `agentstack_guidance_compass`, `agentstack_project_treasury`). Recipe index: `GET /mcp/recipes` · prompt list: `GET /mcp/prompts/list`.

---

## Full action list

**GET /mcp/actions** returns all available `action` values grouped by domain. Use it to discover exact names (e.g. `buffs.apply_temporary_effect`, `projects.create_project_anonymous`) and short summaries. Action count is authoritative at runtime — not hardcoded in markdown docs.

---

## Related docs

- [CONTEXT_FOR_AI.md](CONTEXT_FOR_AI.md) — legacy multi-tool capability map.
- [MCP_AND_ECOSYSTEM.md](../MCP_AND_ECOSYSTEM.md) — index.
- [MCP_OVERVIEW.md](../MCP_OVERVIEW.md) — API endpoints.

**Version:** 0.1 — for agentstack.execute. Update when new domains or actions are added; action list is authoritative at GET /mcp/actions.
