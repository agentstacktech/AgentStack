# AgentStack MCP Capability Matrix

> **Integrator reference.** Action IDs match `GET https://agentstack.tech/mcp/actions`. Tenant-facing catalog only; platform-operator actions are omitted.

- Source: in-process `mcp.routes._build_mcp_actions_catalog_payload`
- Generated: 2026-09-08 20:59 UTC
- Audience: **public (tenant only)**
- Total actions: **568**
- Gene: `repo.plugins.capability_routing.gen1` · `docs.public.classification.gen1`

<!-- BEGIN:AUTOGEN-CAPABILITY-MATRIX -->

## agentnet (40)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agentnet.arb.proof_bundle_for_run` | `—` | Build ERC-8004 proof-to-task bundle for a Fleet run (Arbitrum narrative). |
| `agentnet.arb.status` | `—` | Arbitrum Sepolia rail status (RPC ping, registries). |
| `agentnet.balance` | `—` | Read AGNT ledger balance from the L0 PostgreSQL ledger for a project slice. |
| `agentnet.batch_proof` | `—` | Merkle inclusion paths for a posted AGNT batch (read-only). |
| `agentnet.bnb.apex_jobs_list` | `—` | List agent runs with completed BNB APEX jobs (L0 events). |
| `agentnet.bnb.proof_bundle_for_run` | `—` | L0 proof-to-task bundle for an agent run (read-only). |
| `agentnet.bnb.register_identity` | `—` | Register Fleet agent on BSC ERC-8004 via BNBAgent SDK. |
| `agentnet.bnb.status` | `—` | BSC testnet rail status (RPC ping, registries). |
| `agentnet.bridge.create_intent` | `—` | Create bridge intent (in\|out). REST: POST /api/agentnet/{project_id}/bridge/intents |
| `agentnet.bridge.events` | `—` | List normalized bridge chain events (relay idempotency log). |
| `agentnet.bridge.intents` | `—` | List bridge settlement intents for a project slice. |
| `agentnet.bridge.supply_snapshot` | `—` | L0 AGNT balance sum + in-flight bridge intents + optional EVM ``totalSupply``. |
| `agentnet.chain.finality_probe` | `—` | Read-only finality label probe (Solana adapter stub + Base chain id). |
| `agentnet.chain.intent_status` | `—` | Get chain intent status by intent_id. |
| `agentnet.chain.rails` | `—` | List enabled chain control rails (CCM). |
| `agentnet.chain.submit_intent` | `—` | Submit a chain control intent (Fleet run by default). |
| `agentnet.checkpoint.by_epoch` | `—` | Fetch one checkpoint row by epoch (includes ``batch_roots_json`` when stored). |
| `agentnet.checkpoint.covering_batch` | `—` | Resolve the newest checkpoint epoch that covers a ledger ``batch_id`` (read-only). |
| `agentnet.checkpoint.list_recent` | `—` | List recent AGNT checkpoint epochs for a project (newest first; no ``batch_roots_json``). |
| `agentnet.compute_credits.purchase` | `—` | Purchase compute credits: AGNT user→treasury batch + idempotent ``builder_energy`` top-up. |
| `agentnet.compute_credits.quote` | `—` | Deterministic quote for AGNT cost of compute credits (demo v1 rate table). |
| `agentnet.evidence_receipt` | `—` | Build a self-contained evidence receipt (batch proof + checkpoint prefix witness). |
| `agentnet.funding.offer` | `—` | Get funding offer after R1 USDT payment (opt-in R2 vault). REST: GET funding/offer |
| `agentnet.funding.start_vault_deposit` | `—` | Start opt-in vault deposit intent linked to payment. |
| `agentnet.genome.get` | `—` | Read L0 genome lineage entry for an entity (ecosystem.genome_lineage). |
| `agentnet.genome.verify` | `—` | Verify commit_hash matches canonical GenomeTag JSON. |
| `agentnet.post_batch` | `—` | Post a double-entry AGNT batch (idempotent per ``idempotency_key``). |
| `agentnet.proof_to_task` | `—` | Build ERC-8004 proof-to-task bundle; optional on-chain validation submit. |
| `agentnet.ptr.list_rails` | `—` | List enabled PTR rails (Base, BNB, Arb, Solana) with strategies. |
| `agentnet.receipt.verify` | `—` | Verify an AGNT receipt JSON (canonical hash + optional Merkle proof + optional Ed25519). |
| `agentnet.solana.proof_bundle_for_run` | `—` | Build proof-to-task v2 bundle for a Fleet run (Solana PTR). |
| `agentnet.solana.register_identity` | `—` | Register Fleet agent metadata on Solana attestation program (devnet). |
| `agentnet.solana.status` | `—` | Solana devnet PTR attestation rail status. |
| `agentnet.solana.submit_intent` | `—` | Submit Solana chain control intent (CCM) via gateway. |
| `agentnet.solana.submit_validation` | `—` | Submit task validation to Solana attestation program (best-effort). |
| `agentnet.testnet.faucet_mint` | `—` | Mint AGNT on L0 via testnet faucet (rate-limited). |
| `agentnet.testnet.list_profiles` | `—` | List AgentNet testnet chain profiles (lanes, faucet policy). |
| `agentnet.testnet.run_scenario` | `—` | Run a registered testnet scenario (grant demo, bridge smoke, etc.). |
| `agentnet.vault.deposit_confirm` | `—` | Credit L0 agUSD shares after ERC-4626 deposit (operator/indexer). |
| `agentnet.vault.nav` | `—` | ERC-4626 vault NAV (totalAssets, totalSupply). REST: GET /api/agentnet/{project_id}/vault/nav |

## agents (32)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agents.admin.execute` | `—` | Ecosystem-owner platform actions only (killswitch, bulk_pause, metrics). |
| `agents.approve_run` | `agents_run` | Approve a run stuck in waiting_for_approval and resume execution. |
| `agents.automation_map` | `—` | Fleet automation aggregate: triggers, Logic rule ids, support ownership. |
| `agents.create` | `agents_admin` | Create a new agent row (draft AgentSpec). |
| `agents.create_from_template` | `agents_admin` | Create an agent row from a built-in template (atomic merge on server). |
| `agents.delete` | `agents_admin` | Delete an agent row. |
| `agents.fleet_diagnostics` | `agents_run` | Project fleet health (stuck runs, queue, killswitch) — project read RBAC. |
| `agents.fork` | `agents_admin` | Fork an agent to a new generation (DNA row). |
| `agents.gates` | `agents_run` | Evaluate promotion gates for an agent. |
| `agents.get` | `agents_run` | Get a single agent by UUID (default owner=project; personal rows need owner=user). |
| `agents.health` | `—` | Fleet ECS index health for a project (agents/bots organelle diagnostics). |
| `agents.import_from_asset` | `—` | Create agent from commerce asset extensions.agent_fleet preset. |
| `agents.kill` | `agents_admin` | Set agent lifecycle state to killed (hard stop for new runs). |
| `agents.list` | `agents_run` | List Agents Fleet rows for a project (8DNA entity_type=agent). |
| `agents.list_pending_approvals` | `—` | List runs waiting for approval across project or personal agent scope. |
| `agents.metrics` | `agents_run` | Read rollup metrics for an agent (ecosystem.agents_metrics). |
| `agents.policy_preview` | `agents_run` | Expand AgentPolicy patterns to live MCP actions (preview, no persistence). |
| `agents.promote` | `agents_admin` | Run auto-promotion gates; may set state to live when metrics pass. |
| `agents.reconcile_run` | `agents_run|agents_admin` | Apply reconciler policy to one run (project write/admin RBAC). |
| `agents.rollout_advance` | `agents_admin` | Advance canary rollout step (5→20→50→100). |
| `agents.run` | `agents_run` | Enqueue an agent run (durable work queue). |
| `agents.run_get` | `agents_run` | Get a run row plus normalized RunDetailDTO for cockpit/audit views. |
| `agents.run_with_agnt_credits` | `agents_run` | Demo orchestration: purchase compute credits (AGNT) then enqueue an agent run. |
| `agents.runs_list` | `agents_run` | List agent runs with REST/SDK filters and lightweight audit fields. |
| `agents.stop` | `agents_run` | Cancel a running / queued agent run. |
| `agents.sweep_stale_runs` | `agents_run|agents_admin` | Budgeted stale-run reconcile within one project (write/admin RBAC). |
| `agents.template_preview` | `agents_run` | Return merged AgentSpec-shaped preview for a template (no persistence). |
| `agents.templates_list` | `agents_run` | List built-in Agent Fleet templates (canonical Python catalog). |
| `agents.timeline` | `agents_run` | List recent runs for an agent (uuid + status + timestamps). |
| `agents.traces` | `agents_run` | Return stored run events (trace buffer) for a run. |
| `agents.update` | `agents_admin` | Update an agent — PathAtom section patches via protein DELTA when `path_updates` or `section`+`section_value` is set (write_mode=patch); otherwise `agent_spec` is a full replace (write_mode=replace). |
| `agents.version_timeline` | `—` | List agent version lineage (generation tree — REST GET /timeline parity). |

## ai_builder (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `ai_builder.compose.preview` | `—` | Deterministic compose preview: returns content_sha256 and file paths. |
| `ai_builder.manifest.get` | `—` | Load Unified Application Manifest (UAM v1) for the current project. |
| `ai_builder.manifest.validate` | `—` | Validate a UAM v1 JSON object (structure + catalog + logic templates). |

## analytics (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `analytics.get_metrics` | `—` | Get project performance metrics and analytics data. |
| `analytics.get_usage` | `—` | Get project usage statistics and activity data. |

## apikeys (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `apikeys.create` | `api_keys|apikeys.write` | Create an **agent PAT** (unified user PAT). Postel alias of `user.apikeys.create` with actor_kind=agent. |
| `apikeys.delete` | `api_keys|apikeys.write` | Delete an API key permanently. |
| `apikeys.list` | `api_keys|apikeys.read` | List all API keys for a project. |

## assets (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `assets.create` | `assets_write` | Create a new asset for the project. |
| `assets.delete` | `assets_write` | Delete an asset from the project. |
| `assets.get` | `—` | Get asset details by ID. |
| `assets.list` | `—` | List all assets in the project. |
| `assets.list_presets` | `—` | List deterministic asset wizard presets for the project. |
| `assets.update` | `assets_write` | Update an existing asset. |

## auth (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `auth.channel_identities.list` | `—` | List bot channel identities linked to the authenticated user profile. |
| `auth.channel_identities.revoke` | `—` | Revoke a channel identity link by id (owner-only). |
| `auth.get_profile` | `—` | Get user profile information. |
| `auth.identity.conflicts` | `—` | List project member emails that diverge from ecosystem canonical auth email. |
| `auth.identity.resolve` | `—` | Resolve canonical vs display email for a user (support / debug). |
| `auth.login` | `—` | Login to the system. |
| `auth.register` | `—` | Register a new user. |
| `auth.switch_project` | `—` | Mint a session for an accessible target project (Session OS switch contour). |
| `auth.update_profile` | `—` | Update user profile. |

## bots (23)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `bots.archive` | `—` | MCP tool bots.archive |
| `bots.attach_channel` | `—` | Attach a project messaging connection (telegram, max, whatsapp, …) to a bot fleet row. |
| `bots.broadcast` | `—` | MCP tool bots.broadcast |
| `bots.commands.upsert` | `—` | Merge one or more bot commands by trigger. Never replaces the command list. Use this instead of bots.update when editing /start, /help, or a single handler. |
| `bots.conversations` | `—` | List bot conversations with handoff/needs_reply enrichment for inbox UI. |
| `bots.create` | `—` | MCP tool bots.create |
| `bots.dlq_list` | `—` | MCP tool bots.dlq_list |
| `bots.dlq_replay` | `—` | MCP tool bots.dlq_replay |
| `bots.ensure_mentor_commands` | `—` | Heal mentor /start+/menu+/operator commands + inline buttons (idempotent). |
| `bots.fleet_diagnostics` | `—` | Project bots fleet health (channel health, sessions) — project read RBAC. |
| `bots.get` | `—` | MCP tool bots.get |
| `bots.go_live` | `—` | Activate bot webhooks and set lifecycle live (Telegram / MAX / WhatsApp channels). |
| `bots.health` | `—` | MCP tool bots.health |
| `bots.list` | `—` | MCP tool bots.list |
| `bots.pause` | `—` | MCP tool bots.pause |
| `bots.send_commerce_offer` | `—` | Send a commerce offer card to a bot conversation (listing UUID → outbound attachment). |
| `bots.set_brain` | `—` | MCP tool bots.set_brain |
| `bots.set_handoff` | `—` | MCP tool bots.set_handoff |
| `bots.simulate` | `—` | Dry-run one bot brain turn without outbound. Heavy LLM: at most one per sync agentstack.execute (60s batch; 180s if this is the only heavy step). Suites: one simulate per execute, or options.async + d |
| `bots.staff_reply` | `—` | MCP tool bots.staff_reply |
| `bots.templates` | `—` | MCP tool bots.templates |
| `bots.update` | `—` | Partial bot_spec update (commands, brain, name, …) — same as REST PUT. commands is a keyed list: pass write_mode (merge\|replace\|delete). A short commands array without write_mode merges by trigger and |
| `bots.waba_templates` | `—` | MCP tool bots.waba_templates |

## buffs (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `buffs.apply_buff` | `—` | Apply a buff to an entity (user or project). |
| `buffs.apply_persistent_effect` | `—` | Quickly apply a persistent effect (creates and applies permanent buff). |
| `buffs.apply_temporary_effect` | `—` | Quickly apply a temporary effect (creates and applies buff in one step). |
| `buffs.cancel_buff` | `—` | Cancel a buff in any state (force cancellation). |
| `buffs.create_buff` | `—` | Create a buff template in PENDING state. |
| `buffs.extend_buff` | `—` | Extend the duration of an active buff. |
| `buffs.get_buff` | `—` | Get information about a specific buff. |
| `buffs.get_effective_limits` | `—` | Get effective limits for an entity with all buffs applied. |
| `buffs.list_active_buffs` | `—` | List active buffs for an entity. |
| `buffs.revert_buff` | `—` | Revert an active buff - restore original state from snapshot. |

## business (13)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `business.apply_tariff` | `project_admin` | Apply a tariff template to a business head project. |
| `business.attach_child` | `project_admin` | Attach a child organ project to a business head. |
| `business.command_snapshot` | `mcp_read` | Business command center aggregate (finance + CRM + org). |
| `business.create_composite` | `projects` | Create business head + attach organ children from template. |
| `business.detach_child` | `project_admin` | Detach a child organ from business head. |
| `business.get_org` | `mcp_read` | Get business org config and children index for a head project. |
| `business.link_child` | `—` | Link an existing owned project as a business organ (adopt). |
| `business.list_adopt_candidates` | `—` | List projects eligible for adopt/link into a business head. |
| `business.list_children` | `mcp_read` | List child organ projects for a business head. |
| `business.list_composite_adopt_candidates` | `—` | List standalone projects eligible for adopt during composite business create. |
| `business.list_tariff_templates` | `mcp_read` | List business tariff templates available for a head project. |
| `business.patch_org_settings` | `project_admin` | Patch business head org settings (config.org leaf). |
| `business.transfer_preflight` | `—` | Scan transfer blockers before listing a project for sale. |

## cardgame (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `cardgame.dna.read_rows` | `—` | List up to ``limit`` rows for a cardgame 8DNA entity type using DNA CRUD get (same permission model as the SPA). Use for match room / cell snapshots when building agent context. |
| `cardgame.rules.execute` | `—` | Execute a Logic Engine command for ArcaneStack (e.g. ``cardgame.match.play_card``) with the same payload shape as the game client. Requires authenticated MCP context. |

## commands (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commands.execute` | `logic_write|mcp.execute` | Execute a single Protein Command via universal API. |
| `commands.execute_batch` | `logic_write|mcp.execute` | Execute multiple Protein Commands in batch. |

## commerce (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commerce.ai.generate_products` | `—` | Dry-run AI product generation — returns wizard answer dicts only. Mirrors POST /api/commerce/ai/generate-products. |
| `commerce.ai.magic_fill` | `—` | Parse text into product wizard answers. Mirrors POST /api/commerce/ai/magic-fill. |
| `commerce.coupon.create` | `—` | Create a shop promo code (percent or fixed USDT off). Optional listing_uuids scopes the code to specific listings. Mirrors POST /api/commerce/merchant/coupons. |
| `commerce.coupon.delete` | `—` | Delete a project promo code. Mirrors DELETE /api/commerce/merchant/coupons/{code}. |
| `commerce.coupon.list` | `—` | List project-scoped promo codes from 8DNA commerce.coupon_registry. Mirrors GET /api/commerce/merchant/coupons. |
| `commerce.coupon.update` | `—` | Update a project promo code. Mirrors PUT /api/commerce/merchant/coupons/{code}. |
| `commerce.refund.manual_complete` | `—` | Ecosystem admin: mark fiat/manual refund compensated — revoke entitlements, set order refunded. Mirrors POST /api/admin/commerce/manual-refunds/complete. |
| `commerce.refund.manual_list` | `—` | Ecosystem admin: list refund_requested orders awaiting manual compensation for a buyer commerce slice. Mirrors GET /api/admin/commerce/manual-refunds. |
| `commerce.refund.status` | `—` | Poll refund request + compensation for an order. Mirrors GET /api/commerce/orders/{order_id}/refund-request. |
| `commerce.sell.activate` | `payments|project_admin` | One-shot seller activation: earnings wallet, product seed, public policy, storefront index upsert, optional hosted vitrine. Mirrors POST /api/commerce/sell/activate. |
| `commerce.storefront.health` | `—` | Storefront index + ECS organelle health (admin diagnostics slice). |
| `commerce.storefront.hosted_manifest` | `—` | Read hosted vitrine manifest (bucket, hosted_dirty, merchandising). Mirrors GET /api/commerce/storefront/hosted/manifest. |
| `commerce.storefront.hosted_publish` | `—` | Publish hosted vitrine static bundle + tenant boot JSON when dist is on server. Mirrors POST /api/commerce/storefront/hosted/publish. |
| `commerce.storefront.list` | `—` | List storefront listings via StorefrontReadFacade (ECS organelle). Mirrors GET /api/commerce/storefront/listings. |
| `commerce.storefront.one_click_fill` | `—` | Create listings for eligible catalog assets without active listings. Mirrors POST /api/commerce/storefront/seed/one-click-fill. |
| `commerce.storefront.seed_apply` | `—` | Apply a Storefront Studio seed plan (asset upsert → listing → index). Mirrors POST /api/commerce/storefront/seed/apply. |
| `commerce.storefront.seed_ingest` | `—` | Parse bulk source (csv/json/ai_batch/magic/clone) into ProductSpec rows. Mirrors POST /api/commerce/storefront/seed/ingest. |
| `commerce.storefront.seed_plan` | `—` | Dry-run Storefront Studio seed — compose asset/listing drafts and guidance hints. Mirrors POST /api/commerce/storefront/seed/plan. |
| `commerce.storefront.seed_undo` | `—` | Undo a prior seed run — cancel listings and remove from storefront index. Mirrors POST /api/commerce/storefront/seed/undo. |

## commerce_rest (33)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rest.commerce.cart.apply_coupon` | `—` | Apply or clear coupon on authenticated cart (listing scope enforced). |
| `rest.commerce.cart.checkout` | `—` | Create checkout session from cart (wallet_internal or payments_fiat rail). |
| `rest.commerce.checkout.confirm_session` | `—` | Confirm checkout session (wallet_internal or payments_fiat rail). 402 insufficient_balance, 409 partial_failure — use Idempotency-Key. |
| `rest.commerce.checkout.create_session` | `—` | Create checkout session for listing or cart lines. Send Idempotency-Key header for safe retries. |
| `rest.commerce.checkout.get_session` | `—` | Poll checkout session status until completed/failed/expired. |
| `rest.commerce.delivery.refresh` | `—` | Re-issue signed delivery download token for a fulfilled order line. |
| `rest.commerce.events.refund_stream` | `—` | SSE stream for refund lifecycle (commerce.refund.updated) — buyer/merchant cache refresh. |
| `rest.commerce.guidance.catalog_hints` | `—` | Per-asset listing eligibility and stale price hints. |
| `rest.commerce.guidance.checklist` | `—` | Operator shop setup checklist (catalog + shelf). |
| `rest.commerce.guidance.listing_draft` | `—` | Suggested listing draft defaults for one asset. |
| `rest.commerce.merchant.bulk_deactivate` | `—` | Deactivate multiple listings. |
| `rest.commerce.merchant.bulk_sync_prices` | `—` | Sync all stale listing prices for project. |
| `rest.commerce.merchant.coupon_by_code` | `—` | Update or delete a project promo code. |
| `rest.commerce.merchant.coupons` | `—` | List or create project promo codes (8DNA commerce.coupon_registry). |
| `rest.commerce.merchant.dashboard` | `—` | Seller Command Center KPIs and recommended actions. |
| `rest.commerce.merchant.featured` | `—` | Project featured listing UUIDs in 8DNA storefront config. |
| `rest.commerce.merchant.incoming_orders` | `—` | Incoming sales for seller project. |
| `rest.commerce.merchant.list_listings` | `—` | Admin listings table for seller project (all statuses). |
| `rest.commerce.merchant.reindex` | `—` | Rebuild shop index row for one listing. |
| `rest.commerce.merchant.sync_price` | `—` | Sync listing price from catalog asset. |
| `rest.commerce.merchant.update_listing` | `—` | Update listing status or price (seller admin). |
| `rest.commerce.my_purchases` | `—` | Buyer purchases BFF — orders merged with active entitlements and delivery URLs. |
| `rest.commerce.orders.refund_request` | `—` | Request refund; wallet rail auto-compensates USDT. Fiat (payments_fiat) skips wallet credit — manual ops via admin manual-refunds. |
| `rest.commerce.orders.refund_status` | `—` | Poll refund request status and compensation snapshot for an order. |
| `rest.commerce.participant.grant_holding` | `—` | Operator grant inventory to project member (requires write). |
| `rest.commerce.participant.holdings` | `—` | User holdings in a project (participant plane, not catalog). |
| `rest.exchange.execute` | `—` | Execute cross-project currency/asset exchange. |
| `rest.exchange.quote` | `—` | Cross-project exchange quote (not protein command_type exchange). |
| `rest.marketplace.accept_listing` | `—` | Accept deal / settle listing (may trigger internal payment flow). |
| `rest.marketplace.close_auction` | `—` | Close auction listing. |
| `rest.marketplace.create_listing` | `—` | Create listing (types: sale, buy, exchange, auction). Not an MCP action. |
| `rest.marketplace.list_listings` | `—` | List marketplace listings with filters. |
| `rest.marketplace.place_bid` | `—` | Place bid on auction listing. |

## context (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `context.get` | `rag.read` | Prefetch RAG + memory context for a task. |

## crm (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `crm.create_company` | `—` | Create a CRM company for a project. |
| `crm.create_deal` | `—` | Create a CRM deal in the default pipeline. |
| `crm.erase_contact` | `—` | Anonymize/erase a CRM contact (GDPR). |
| `crm.export_contact` | `—` | Export GDPR bundle for a CRM contact. |
| `crm.get_contact_360` | `—` | Contact 360 view: profile, deals, unified timeline. |
| `crm.get_crm_config` | `—` | Project CRM config (saved_views, settings blob). |
| `crm.get_deal_timeline` | `—` | Activity timeline for a CRM deal (notes, tasks linked to deal_id). |
| `crm.import_contacts` | `—` | Batch import CRM contacts with email dedupe. |
| `crm.list_board` | `—` | Pipeline kanban board with stage columns and deals. |
| `crm.list_companies` | `—` | List CRM companies for a project. |
| `crm.list_contacts` | `—` | List CRM contacts for a project (paginated BFF). |
| `crm.log_activity` | `—` | Log a CRM activity (note, call, task, etc.). |
| `crm.magic_fill` | `—` | AI/heuristic autofill for quick-create (dry-run, no write). |
| `crm.merge_contacts` | `—` | Merge duplicate contact into primary (absorb duplicate). |
| `crm.move_deal_stage` | `—` | Move a deal to another pipeline stage. |
| `crm.patch_crm_config` | `—` | Patch project CRM config (writers may update saved_views only). |
| `crm.search` | `—` | Search CRM contacts by query string. |
| `crm.suggest_field` | `—` | Suggest values for a CRM field from partial context. |
| `crm.upsert_contact` | `—` | Create or update a CRM contact (dedupe by email). |

## data_access (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `data_access.apply_defaults_template` | `data_access_admin` | Copy missing `resources.<name>` entries from the global template into `target_project_id`'s field_access_policy. Does not overwrite existing keys. Cannot target ecosystem project id=1. |
| `data_access.check_field` | `—` | Check whether a specific role can read/write a field in a resource. |
| `data_access.get_defaults_template` | `—` | Read the global Field Access Policy template from ecosystem project DNA (`config.field_access_defaults`: default_access, globals, resources). |
| `data_access.get_policy` | `—` | Retrieve the current field-level access policy for a project. |
| `data_access.get_triggers` | `—` | Read FAP v1.2 ``field_triggers`` for a project (same JSON as ``field_access_policy.field_triggers``). Keyed by resource → pattern → list of trigger definitions. See FIELD_ACCESS_POLICY.md — Field Trig |
| `data_access.set_defaults_template` | `data_access_admin` | Write the global FAP template on ecosystem project (admin tooling). |
| `data_access.set_policy` | `data_access_admin` | Create or update the field-level access policy for a project resource. |
| `data_access.set_triggers` | `data_access_admin` | Upsert ``field_triggers`` for one resource. **triggers** is a map ``field_pattern → [trigger_def, …]`` (patterns like ``status``, ``config.**``, ``*``). |
| `data_access.test_mask` | `—` | Preview what fields a given role can read/write in a resource. |

## diagnostics (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `diagnostics.gene_token_query` | `—` | GTPI bool query against process-local gene token lexicon. |
| `diagnostics.neural_graph` | `—` | Neural Visualizer Gen2 snapshot: topology, heat planes (protein/api/dna_query/gene), composition edges, hot_region_hints. Always includes heat_meta (worker layout honesty). Process-local heat; Tier F |
| `diagnostics.promote_hot_gene` | `—` | Promote a genetic tag's path prefixes into HotProteinRegion for an entity. Modes: warm, pin, prefetch_only. Tier F materialize blocked. |

## discovery (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `discovery.get_platform_surfaces` | `—` | List canonical platform routes from generated platform-surface-audit.json. |
| `discovery.job_status` | `—` | Poll an async agentstack.execute job (options.async=true). Cheap. Use after a multi-step heavy batch; do not re-run bots.simulate to check status. |
| `discovery.list` | `—` | List all MCP actions (alias for GET /mcp/actions catalog). Includes execute_budget (sync 60s / one heavy LLM step) and write_modes (merge\|replace\|append\|delete — pass write_mode on collection/body wri |

## dna (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `dna.lineage.get_ancestors` | `—` | Walk ancestor chain for an 8DNA row (materialized path or scoped CTE fallback). |
| `dna.lineage.get_descendants` | `—` | List descendant rows under a parent UUID within project scope. |

## docs_nav (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `docs_nav.resolve` | `—` | Resolve a genetic navigation tag to AI_INDEX path(s) from TAG_CATALOG. For code/docs navigation only — for runtime MCP tools use discover/by_intent. |
| `docs_nav.search` | `—` | BM25-lite search over AI navigation catalog (map/index triggers). Returns genetic tags + AI_INDEX paths. Not for selecting runtime MCP tools. |

## energy (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `energy.get_balance` | `—` | LLM prepaid energy balance for user or project pool. |
| `energy.list_packs` | `—` | Available LLM energy pack tiers (small/medium/large) and USD prices. |
| `energy.purchase` | `—` | Purchase energy pack via unified finance.pay (wallet) or payments.create (card). |

## finance (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `finance.activity` | `—` | Unified wallet activity feed. |
| `finance.dashboard_bundle` | `—` | Finance hub landing aggregate (portfolio + billing + activity preview). |
| `finance.pay.execute` | `—` | Execute a quoted pay intent with idempotency key. |
| `finance.pay.quote` | `—` | Quote a unified pay intent (energy_pack, subscription, compute_credits, fund, contribute). |
| `finance.portfolio` | `—` | User finance portfolio aggregate (three rails + JSON wallets). |
| `finance.project.contribute` | `—` | Member contribution: USD only from personal ecosystem wallet (project_id=1) to project treasury. Not for custom/game currencies. |
| `finance.project.contributions.list` | `—` | List member contributions for a project (FAP-masked). |
| `finance.project.fund` | `—` | Fund a project via ecosystem USD transfer. |
| `finance.project.portfolio` | `—` | Project business portfolio (operating + treasury + AGNT). |

## generation (20)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `generation.approve` | `project_admin` | Mark manual generation approval on anchor and re-run gate evaluation. |
| `generation.canary.abort` | `project_admin` | Abort canary rollout and restore previous production pointer. |
| `generation.canary.advance` | `project_admin` | Advance canary rollout to the next traffic weight step. |
| `generation.checkpoint` | `—` | Materialise merged production/sandbox DNA into a checkpoint generation (rollback SoT). Use before risky bulk edits; rollback via generation.rollback. |
| `generation.diff` | `mcp_read` | Structured diff between two resolved generation entity states (same table/project). |
| `generation.diff_vs_prod` | `mcp_read` | Diff stable production head vs pending sandbox head. Includes agent_context summary for diagnosis (counts, domains, sample paths). |
| `generation.fork` | `project_admin` | Fork production project into a new sandbox environment generation. |
| `generation.gates` | `mcp_read` | Run generation gate chain for an environment (schema, conflict, health, soak, metrics, optional manual). |
| `generation.hosting.release` | `project_admin` | Snapshot live hosting workspace into an immutable release labeled with sandbox generation. Does not swap canonical /s/ URL — follow with hosting.release.promote. |
| `generation.list` | `mcp_read` | List sandbox environment anchors for a project (names, status, CalVer, auto_created flags). |
| `generation.notify` | `project_admin` | Manual generation notify — webhooks, audit log, optional in-app notification. |
| `generation.promote` | `project_admin` | Promote a sandbox environment to production (runs promotion checks first). |
| `generation.queue` | `—` | Promotion queue: pending sandbox generations with gate evaluation (REST parity). |
| `generation.realign_to_prod` | `project_admin` | Revert pending sandbox DNA toward stable production by applying inverse diff (leaf jsonb_set / path delete on sandbox anchor — no full-blob wipe). Optional path_prefixes for partial revert. |
| `generation.rollback` | `—` | Open a checkpoint as a new sandbox generation (draft). Promote the returned rollback_uuid via generation.promote to restore production DNA. |
| `generation.settings.get` | `mcp_read` | Read per-project auto_generation settings (REST parity: GET /api/generations/{id}/settings). |
| `generation.settings.patch` | `project_admin` | PATCH auto_generation settings on the production project slice. Enable auto_generation_mode so tracked MCP mutations auto-fork to sandbox. REST parity: PATCH /api/generations/{id}/settings. |
| `generation.status` | `mcp_read` | Lightweight generation + canary status for UI banners and agents. |
| `generation.supply_status` | `—` | Tenant generation supply snapshot: owner tier, open-env quota usage, auto_generation settings, pending/canary state. Alias for agents (same SoT as GET /api/sandbox/limits + generation.status). |
| `generation.timeline` | `mcp_read` | Promotion history for a project: completed, active canary (rolling_out), failed, and aborted requests (newest first). Items include strategy, traffic_weight, calver_label when recorded. |

## guidance (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `guidance.complete_step` | `—` | Mark a guidance step complete with optional artifact after server verify gates. Agent parity with SPA completePathStep. |
| `guidance.funnel_stats` | `—` | O(1) in-process Compass funnel counters for messaging-channel-bot: path_started, per-task completions, channel mix, dual_channel_attach. No database scan. |
| `guidance.get_session` | `—` | Get one guidance session by id for the authenticated user. |
| `guidance.list_active_paths` | `—` | Alias for guidance.list_active_sessions — active Compass paths with percent and state. Use for agent resume decisions. |
| `guidance.list_active_sessions` | `—` | List active guidance sessions for the authenticated user in a project. |
| `guidance.list_capability_tasks` | `—` | List Platform Task Capability (PTC) atoms from platform fixture catalog Use before Compass playbooks with kind=capability or agentstack.execute task hints. |
| `guidance.match_playbook` | `—` | Match natural-language goal text to a Platform Compass playbook id using bundled intent patterns + RU/EN synonyms. Returns verifyKinds for bot/commerce paths. |
| `guidance.path_status` | `—` | Snapshot Compass verify gates for a project: botExists, botLive, botChannelAttached, hostedVitrinePublished. Use after playbook steps or before go-live checks. |
| `guidance.record_step` | `—` | Patch an active guidance session state (answers, currentNodeId, completedNodeIds, percent). Agent parity with SPA pathServerSync step PATCH. |
| `guidance.start_path` | `—` | Alias for guidance.start_session — start a Platform Compass guided path on the server. Prefer this name in agent playbooks; identical parameters and response. |
| `guidance.start_session` | `—` | Start a Platform Compass guidance session on the server (8DNA data.guidance). Use with messaging-channel-bot for cross-device resume. |

## hosting (20)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `hosting.bucket.file.get` | `—` | Read a single bucket file (text or base64, max 2MB). |
| `hosting.bucket.file.rename` | `—` | Rename or move a file within a bucket. |
| `hosting.bucket.files.bulk_delete` | `—` | Delete multiple files by path list or prefix. |
| `hosting.bucket.files.list` | `—` | List files in a hosting bucket (optional prefix, cursor pagination, optional q path substring filter). |
| `hosting.demo.status` | `—` | Public demo hosting pool status (enabled, depth, sandbox project id). |
| `hosting.demo_store.status` | `—` | Golden promo demo-store vitrine: catalog row count on sandbox, published build id, manifest bucket (`frontend.commerce.hosted_storefront.gen1`). |
| `hosting.deploy_files` | `—` | Batch-upload files to a bucket and optionally publish (max 50 files per call). |
| `hosting.files.put` | `—` | Upload or replace a file in a hosting bucket. |
| `hosting.gc.ix` | `—` | Prune orphan hosting_buckets/_ix symlinks (admin diagnostics broken_ix). |
| `hosting.project.set_primary_site` | `—` | Set the project's primary public site URL when multiple sites exist (PH-18). |
| `hosting.project.status` | `—` | Project hosting plane: Host/Sell/Scale ladder, sites summary, next_actions. |
| `hosting.release.clone_site` | `—` | Clone a new site from a release snapshot. |
| `hosting.release.delete` | `—` | Delete an unpinned hosting release (optional bucket purge). |
| `hosting.release.list` | `—` | List site release history (newest first). |
| `hosting.release.promote` | `—` | Promote a release snapshot to the live site bucket and publish. |
| `hosting.release.snapshot` | `—` | Create immutable snapshot (archive bucket copy) for a site. |
| `hosting.site.edge_health` | `—` | Check nginx edge readiness (symlinks, index.html) for a hosting bucket. |
| `hosting.site.quick_start` | `—` | Create bucket, upload HTML index, optional publish; returns public /s/{project_id}/{bucket_name}/ URL. |
| `hosting.site.resolve` | `—` | Resolve hosting bucket_name to bucket_id and public URL hints. |
| `hosting.storage.import_folder` | `—` | Import a project storage folder into a hosting bucket. |

## integrations (50)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `integrations.activate_webhook` | `—` | Register provider inbound webhook for a connection (post-OAuth or manual retry). |
| `integrations.connection_health` | `mcp_read` | Read connection health snapshot (public spec only). |
| `integrations.connector_schema` | `mcp_read` | Provider setup schema metadata for wizard/agents. |
| `integrations.create_connection` | `—` | Create an integration connection (REST POST /api/integrations/connections parity). Prefer integrations.install_recipe for packaged inbound webhooks. |
| `integrations.create_scenario` | `—` | Create a draft integration scenario with backing logic rule. |
| `integrations.dynamic_fields` | `—` | List dynamic field sources for an integration app (schema-only in Phase 1). |
| `integrations.export_connection` | `mcp_read` | Export connection public spec + config (secrets redacted). |
| `integrations.export_connection_manifest` | `mcp_read` | Export project integration connections as JSON manifest for agent tool loops. |
| `integrations.export_scenario` | `—` | Export portable scenario bundle (rule document + field maps, secrets redacted). |
| `integrations.generate_scenario` | `—` | NL → RuleDocumentV1 draft for integration scenario (copilot stub). |
| `integrations.generate_signing_secret` | `project_admin` | Rotate signing_secret server-side without reading the old value. |
| `integrations.get_app` | `—` | Get a single integration app definition by provider id. |
| `integrations.get_diagnostics` | `mcp_read` | Integration Hub diagnostics rollup and worker flags. |
| `integrations.get_hubspot_relay_stats` | `—` | HubSpot platform relay rollup (7d success/denied/no-connection/missing portal). |
| `integrations.hydrate_triggers` | `—` | Hydrate integration trigger registry from connection DNA (cold start recovery). |
| `integrations.import_connection` | `project_admin` | Import connection from export JSON (optionally with secrets). |
| `integrations.import_make_preview` | `mcp_read` | Dry-run preview: map Make scenario JSON to recipe + logic block draft. |
| `integrations.import_n8n_preview` | `—` | Preview n8n workflow JSON as recipe + logic block draft. |
| `integrations.import_zapier_preview` | `mcp_read` | Dry-run preview: map Zapier export JSON to recipe + logic block draft. |
| `integrations.install_recipe` | `project_admin` | Install a recipe: creates connection + declarative logic rule. |
| `integrations.list_apps` | `—` | List integration app catalog entries (connectors + recipe metadata). |
| `integrations.list_connections` | `mcp_read` | List integration connections for project or personal (ecosystem user) scope. |
| `integrations.list_dlq_deliveries` | `—` | List integration outbound deliveries in DLQ or terminal failure state. |
| `integrations.list_inbox_events` | `mcp_read` | List durable integration inbox audit rows for a project. |
| `integrations.list_issues` | `mcp_read` | Cluster failed outbound deliveries into issues (Hookdeck-style). |
| `integrations.list_module_registrations` | `—` | List module registrations persisted on connection DNA rows. |
| `integrations.list_modules` | `—` | List reusable sub-scenario module stubs. |
| `integrations.list_recipes` | `mcp_read` | List integration recipe templates (Stripe, GitHub, Telegram, etc.). |
| `integrations.list_scenario_runs` | `—` | List recorded test/run history for a scenario. |
| `integrations.list_scenarios` | `—` | List integration scenarios for a project scope. |
| `integrations.migrate_legacy_webhooks` | `mcp_read` | Copy projects.config.webhooks into integration_connection rows. |
| `integrations.oauth_begin` | `—` | Start OAuth 2.0 PKCE flow for an integration connection (returns authorization_url). |
| `integrations.poll_connection` | `mcp_read` | Run connector polling for a connection (cursor watermark in config.poll_state). |
| `integrations.preview_scenario_maps` | `—` | Evaluate scenario field maps against a sample payload (secrets redacted). |
| `integrations.process_inbox_batch` | `mcp_read` | Process durable inbound integration inbox (worker-style batch). |
| `integrations.process_outbound_batch` | `mcp_read` | Process outbound webhook delivery work queue (webhooks.outbound batch). |
| `integrations.process_polling_batch` | `—` | Poll due integration connections (config.poll_enabled, cursor watermark). |
| `integrations.publish_scenario` | `—` | Publish scenario (live) and enable backing logic. |
| `integrations.rebind_triggers` | `—` | Re-sync enabled logic rule triggers for a project or all connection projects. |
| `integrations.refresh_token` | `—` | Refresh OAuth access token for a connection when expired (protected storage). |
| `integrations.register_module` | `—` | Register a reusable sub-scenario module stub on a parent connection. |
| `integrations.replay_delivery` | `mcp_read` | Replay outbound delivery by durable delivery_id (execution log). |
| `integrations.replay_delivery_url` | `mcp_read` | Replay outbound POST to an explicit target_url (same as REST deliveries/replay query). |
| `integrations.replay_inbox` | `—` | Replay a durable inbox event through the test-hook intake path. |
| `integrations.rotate_secret` | `project_admin` | Rotate a protected secret key for a connection. |
| `integrations.save_scenario_draft` | `—` | Save scenario draft (spec and/or logic patch). Parity with REST POST /scenarios/{id}/draft. |
| `integrations.test_hook` | `mcp_read` | Dry-run inbound hook verify + normalize (sync dispatch to logic). |
| `integrations.test_scenario_step` | `—` | Dry-run a scenario step via logic simulation. |
| `integrations.unpublish_scenario` | `—` | Unpublish scenario (pause) and disable backing logic. Parity with REST unpublish. |
| `integrations.update_connection` | `—` | Patch integration connection metadata (label, status, config, direction). |

## knowledge (31)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `knowledge.access.grant` | `—` | MCP tool knowledge.access.grant |
| `knowledge.access.list` | `—` | MCP tool knowledge.access.list |
| `knowledge.access.revoke` | `—` | MCP tool knowledge.access.revoke |
| `knowledge.config.get` | `—` | MCP tool knowledge.config.get |
| `knowledge.config.patch` | `—` | Patch knowledge DNA config + safety playbooks (same as PATCH /knowledge/config). gene_pack merges by name; safety_playbooks default write_mode is patch (per-playbook overlay — other playbook ids are k |
| `knowledge.content.delete` | `—` | Deactivate or hard-delete a KB document by source_doc_id. Same as DELETE /knowledge/content/{id}. |
| `knowledge.content.get` | `—` | Get one KB document by source_doc_id (chunks + joined text). Always call this before knowledge.content.patch replace. text uses the same \n\n join as list; check text_chars before rewriting. |
| `knowledge.content.list` | `—` | List operator KB documents. Each item includes title, source_doc_id, chunk_count, and joined text (same as GET /knowledge/content). |
| `knowledge.content.patch` | `—` | Patch a KB document: metadata (title, gene_stems, …) and/or body. Body write_mode is replace (full text), append, or prepend — never a silent section splice. Same as PATCH /knowledge/content/{id}. |
| `knowledge.eval.run` | `—` | MCP tool knowledge.eval.run |
| `knowledge.eval.run_all` | `—` | MCP tool knowledge.eval.run_all |
| `knowledge.export` | `—` | MCP tool knowledge.export |
| `knowledge.gc.reconcile` | `—` | MCP tool knowledge.gc.reconcile |
| `knowledge.gc.status` | `—` | MCP tool knowledge.gc.status |
| `knowledge.health` | `—` | MCP tool knowledge.health |
| `knowledge.import` | `—` | MCP tool knowledge.import |
| `knowledge.journal.list` | `—` | MCP tool knowledge.journal.list |
| `knowledge.kb.heal_index` | `—` | Realign DNA storage_ref.sha256 to on-disk sqlite for a KB collection (no wipe). Fixes rag_index_sha256_mismatch. Default collection_id=content_registry. |
| `knowledge.kb.ingest` | `—` | MCP tool knowledge.kb.ingest |
| `knowledge.playground` | `—` | Full bot simulate with RAG/safety/gene-lock trace (REST playground). Heavy LLM: one per sync agentstack.execute. Prefer over raw rag.search for mentor fidelity. Same budget as bots.simulate. |
| `knowledge.policy_templates.apply` | `—` | Apply a platform policy template onto the tenant: plan_instructions append (marker [policy:id]), merge crisis match_any (keep tenant template), union gene_pack.situational_needles, optional safety_fai |
| `knowledge.policy_templates.list` | `—` | Platform policy templates (synthesis / crisis / situational needles / gate). Same catalog as GET /knowledge/policy-templates. Apply writes 8DNA via existing prompt+config PATCH — not a second bus. |
| `knowledge.policy_templates.status` | `—` | Read-only policy snapshot: synthesis [policy:id] markers in plan_instructions, playbook ids, gate mode, situational needle count. Same as GET /knowledge/policy-templates/status. |
| `knowledge.principal.simulate` | `—` | MCP tool knowledge.principal.simulate |
| `knowledge.project_ai_keys.patch` | `—` | Patch tenant project AI keys in project.protected (KeySelector). Same storage as PUT /api/projects/{id}/ai-keys — no secret values in GET. |
| `knowledge.project_ai_keys.status` | `—` | AI key presence + ai_settings flags (no secret values). |
| `knowledge.project_secrets.apply` | `—` | Apply Unity tenant secrets: OpenAI → project.protected, GetCourse/MAX → Integration Hub protected. Pass openai/getcourse/max fields. |
| `knowledge.prompt.get` | `—` | Grounded assistant preamble + plan_instructions (same as GET /knowledge/prompt). Returns full strings plus system_preamble_chars / plan_instructions_chars. Always GET before knowledge.prompt.patch rep |
| `knowledge.prompt.patch` | `—` | Patch grounded assistant system_preamble and/or plan_instructions (same as PATCH /knowledge/prompt). omit-unset keeps unsent keys; a sent field is the full string. Writes prompt_settings leaf via patc |
| `knowledge.registry.list` | `—` | MCP tool knowledge.registry.list |
| `knowledge.registry.upsert` | `—` | MCP tool knowledge.registry.upsert |

## logic (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `logic.attach_template` | `logic_write` | Install a template from the built-in catalog into the current project. |
| `logic.create` | `logic_write` | Create new Logic Engine rule for project. |
| `logic.delete` | `logic_write` | Delete a Logic Engine rule. |
| `logic.diff_versions` | `—` | Diff two rule snapshots (F2.9). |
| `logic.dry_run` | `logic_write` | Simulate a logic rule execution without side effects (F2.5). |
| `logic.execute` | `logic_write` | Execute a Logic Engine rule immediately. |
| `logic.export_json` | `—` | Export a logic rule as JSON for backup / cross-project copy. |
| `logic.flush_batch` | `logic_write` | Force flush logic batch for a project (saves all accumulated rules immediately) |
| `logic.get` | `—` | Get detailed information about a Logic Engine rule. |
| `logic.get_commands` | `—` | Get list of available commands for triggers. |
| `logic.get_processors` | `—` | Get list of available processors (same as processors.list but in logic context). |
| `logic.import_json` | `logic_write` | Import a logic rule from JSON (creates a new rule). |
| `logic.install_blueprint` | `logic_write` | Install a catalog blueprint into the project (alias of attach_template with field_values). |
| `logic.list` | `—` | List all Logic Engine rules for project. |
| `logic.list_versions` | `—` | List historical snapshots stored on a rule (F2.9). |
| `logic.mcp_actions_catalog` | `—` | List MCP actions addressable from logic rules. |
| `logic.restore_version` | `logic_write` | Roll a rule back to a prior snapshot (F2.9). |
| `logic.signals_catalog` | `—` | List signal channels the logic engine can subscribe to. |
| `logic.update` | `logic_write` | Update existing Logic Engine rule. |

## mentor (8)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `mentor.eval.retrieval` | `—` | MCP tool mentor.eval.retrieval |
| `mentor.eval.run` | `—` | MCP tool mentor.eval.run |
| `mentor.eval.run_all` | `—` | MCP tool mentor.eval.run_all |
| `mentor.export` | `—` | MCP tool mentor.export |
| `mentor.journal.list` | `—` | MCP tool mentor.journal.list |
| `mentor.kb.ingest` | `—` | MCP tool mentor.kb.ingest |
| `mentor.principal_link.bind_code` | `—` | MCP tool mentor.principal_link.bind_code |
| `mentor.principal_link.upsert` | `—` | MCP tool mentor.principal_link.upsert |

## messaging (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `messaging.get_delivery_health` | `—` | Messaging ops status snapshot (admin). |
| `messaging.list_recent_deliveries` | `—` | Recent messaging delivery log rows (admin). |
| `messaging.list_suppressions` | `—` | List ecosystem email suppression list (bounces/complaints). |
| `messaging.remove_suppression` | `—` | Remove an email from the ecosystem suppression list (admin). |
| `messaging.send_test_email` | `—` | Send ecosystem test email (admin). |

## notifications (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `notifications.cancel_push` | `—` | Cancel pending push items by correlation key prefix. |
| `notifications.list_prefs` | `—` | Messenger prefs plus category×channel matrix and enrolled bot sources. |
| `notifications.register_category` | `—` | Register a logical push category in user messenger prefs (persisted). |
| `notifications.send` | `—` | Deprecated alias — use notifications.send_push or integrations webhooks. |
| `notifications.send_push` | `—` | Enqueue OS web push for a user (self or admin). |
| `notifications.subscribe_push` | `—` | Returns VAPID status; browser must still call PushManager.subscribe. |
| `notifications.update_prefs` | `—` | Patch messenger prefs and/or category×channel notification matrix. |

## organelle (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `organelle.execute` | `—` | Invoke a registered organelle op via dispatch (ring_pool / cell_store today; storage & work_queue via related tools). |

## payments (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `payments.create` | `payments` | Create a new payment transaction. |
| `payments.get` | `payments` | Get detailed payment information by payment ID. |
| `payments.get_balance` | `payments` | Get current wallet balance for a project. |
| `payments.list_transactions` | `payments` | List all payment transactions for a project. |
| `payments.refund` | `payments` | Refund a completed payment. |

## processors (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `processors.execute` | `logic_write` | Execute a processor directly. |
| `processors.get_metadata` | `—` | Get detailed metadata for a specific processor. |
| `processors.list` | `—` | List all available processors with their metadata. |

## projects (16)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `projects.add_user` | `project_admin` | Add a user to a project (requires Professional subscription or ecosystem context and manage_users/owner). |
| `projects.create_project` | `—` | Create a new project. |
| `projects.create_project_anonymous` | `—` | Create a new anonymous project for MCP clients without prior authentication. |
| `projects.delete_project` | `project_admin` | Delete a project (PSDP). |
| `projects.execute_deletion` | `project_admin` | Execute scheduled or immediate deletion with execution token. |
| `projects.get_data` | `—` | Read one nested path under project.data (REST GET /projects/{id}/data?path=). Use before projects.patch_data when you need the current leaf. |
| `projects.get_deletion_inventory` | `—` | Pre-delete inventory: blockers, warnings, and execution_token for safe delete. |
| `projects.get_project` | `—` | Get detailed information about a specific project. |
| `projects.get_projects` | `—` | Get list of projects for the current user. |
| `projects.get_stats` | `—` | Get project statistics. |
| `projects.get_users` | `—` | Get list of users in a project. |
| `projects.patch_data` | `—` | Leaf SET on project.data (REST PATCH /projects/{id}/data). write_mode=replace sets the path; merge overlays an object; delete removes it. Do not use projects.update_project for nested bot commands or |
| `projects.remove_user` | `project_admin` | Remove a user from a project (requires Professional subscription or ecosystem context). |
| `projects.schedule_deletion` | `project_admin` | Schedule project deletion after inventory + execution token. |
| `projects.update_project` | `project_admin` | Update an existing project. |
| `projects.update_user_role` | `project_admin` | Update a user's role in a project (requires manage_users or owner; cannot assign owner via this tool). |

## rag (16)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rag.collection_create` | `rag.write` | Create a new RAG knowledge base collection for the current project. |
| `rag.collection_delete` | `rag.write` | Delete a RAG collection and all its documents. This is irreversible. |
| `rag.collection_list` | `rag.read` | List all RAG collections for the current project. |
| `rag.document_add` | `rag.write` | Add a text document to a RAG collection. |
| `rag.document_add_batch` | `—` | Add multiple documents to a RAG collection in one call. |
| `rag.document_delete` | `rag.write` | Remove a document (all its chunks) from a collection by source_doc_id. |
| `rag.document_list` | `rag.read` | List document chunks stored in a collection. |
| `rag.export` | `—` | Export accessible collection chunks (access-tier filtered) as JSON documents. |
| `rag.health` | `—` | RAG persistence mode and collection stats for the current project scope. |
| `rag.ingest_from_storage` | `—` | Ingest text extracted from a Storage file into a RAG collection. |
| `rag.ingest_job_status` | `—` | Poll async batch ingest job status (work_queue rag_ingest). |
| `rag.memory_add` | `rag.write` | Store a conversation turn in AI memory. |
| `rag.memory_get` | `rag.read` | Get the most recent conversation turns from memory (chronological order). |
| `rag.memory_search` | `rag.read` | Semantically search past conversation turns. |
| `rag.search` | `rag.read` | Semantic search over a RAG collection. |
| `rag.warm` | `—` | Pre-load collection vectors into process L1 before search traffic. |

## rbac (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rbac.assign_role` | `—` | Assign a role to a user in a project. Requires manage_users or owner. Cannot assign owner via this tool. |
| `rbac.check_permission` | `—` | Check if a user has a permission in a project. If user_id is omitted, checks current user. |
| `rbac.get_roles` | `—` | Get list of roles for a project (system + custom). Returns id, name, permissions_bitmap, permissions, is_system. |
| `rbac.revoke_role` | `—` | Revoke a role from a user in a project. User will get default viewer role. Requires manage_users or owner. Cannot revoke owner. |

## safety (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `safety.evaluate` | `—` | MCP tool safety.evaluate |

## scheduler (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `scheduler.cancel_task` | `scheduler` | Cancel/remove a scheduler task (removes from pool and disables in database). |
| `scheduler.clear_pool_tasks` | `scheduler` | Clear all tasks from the execution pool (does not delete from database). |
| `scheduler.create_task` | `scheduler` | Create a new scheduled task for automated execution. |
| `scheduler.delete_pool_task` | `scheduler` | Remove a specific task from the execution pool (does not delete from database). |
| `scheduler.execute_task` | `scheduler` | Execute a scheduled task immediately, bypassing the cron schedule. |
| `scheduler.get_pool_task_details` | `scheduler` | Get detailed information about a specific task from the execution pool. |
| `scheduler.get_pool_tasks` | `scheduler` | Get all tasks from the execution pool (in-memory task queue). |
| `scheduler.get_task` | `scheduler` | Get detailed information about a scheduled task by ID. |
| `scheduler.list_tasks` | `scheduler` | List all scheduled tasks for a project. |
| `scheduler.refresh_pool` | `scheduler` | Force a refresh of the scheduler pool from the database. |
| `scheduler.update_task` | `scheduler` | Update an existing scheduler task (requires write permission). |

## seo (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `seo.cache.clear` | `—` | Invalidate neural SEO meta + robots/sitemap caches; optionally ping IndexNow. |
| `seo.health.check` | `—` | SEO registry version, indexable path count, and hosting funnel readiness. |
| `seo.meta.get` | `—` | Fetch dynamic meta tags for a public marketing path (title, description, OG, JSON-LD). |

## services (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `services.apply_delivery_slot` | `—` | Grant phase-1 studio delivery slot on tenant head project (data.ecosystem.buffs[] protected). After signed main SoW. |
| `services.diagnostic_sow_draft` | `—` | Render a studio diagnostic (kickoff) SoW markdown from the services catalog fixture. Staff-only on ecosystem project 1. |
| `services.kickoff_slot_status` | `—` | Read kickoff slot status for CRM contact (optional payer user buff merge). |

## social (66)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `social.channel_invites.accept` | `social_read` | Accept a channel invite from inbox. |
| `social.channel_invites.decline` | `social_read` | Decline a channel invite. |
| `social.channel_invites.direct` | `social_read` | Create direct channel invite to a user (owner). |
| `social.channel_invites.inbox` | `social_read` | List pending channel invites for the current user. |
| `social.channel_invites.link_token` | `social_read` | Create shareable link token for a channel (owner). |
| `social.channel_invites.redeem` | `social_read` | Redeem channel invite token. |
| `social.channels.delete` | `social_read` | Delete a channel (owner) and unpublish from public index. |
| `social.channels.get` | `social_read` | Get one channel if the current user may access it. |
| `social.channels.list` | `social_read` | List all channels in the home project. |
| `social.channels.register` | `social_read` | Create a new messenger channel (group chat, DM, or broadcast). |
| `social.channels.update` | `social_read` | Update channel metadata (owner). |
| `social.chat.crdt_state` | `social_read` | Read the current CRDT snapshot + raw updates for a channel. |
| `social.chat.crdt_update` | `social_read` | Apply a Y.js CRDT update to a channel's shared document (bodies / pins / deletes). |
| `social.chat.delta` | `social_read` | Unified incremental delta for a messenger channel — one call returns new messages, |
| `social.chat.history` | `social_read` | Read chat history from a messenger channel (AI-accessible). |
| `social.chat.index_get` | `social_read` | Get the current user's messenger sidebar index — list of channels with pinned/order metadata. |
| `social.chat.index_put` | `social_read` | Replace the messenger sidebar index (subset-as-delete with If-Match on REST). |
| `social.chat.message_delete` | `social_read` | Delete a chat message (author or channel owner per server rules). |
| `social.chat.message_edit` | `social_read` | Edit own chat message (author only). |
| `social.chat.pin` | `social_read` | Pin a message in a channel. |
| `social.chat.post` | `social_read` | Send a chat message to a messenger channel as the AI agent (authenticated user). |
| `social.chat.presence_online` | `social_read` | List user IDs currently online (active SSE stream-relay connection) in the home project. |
| `social.chat.reaction` | `social_read` | Apply one server-authoritative reaction on a message (immutable cell per user). |
| `social.chat.read_get` | `social_read` | Get last-read state for a channel for the current user. |
| `social.chat.read_set` | `social_read` | Update last-read pointer (read receipt) for a channel. |
| `social.chat.unpin` | `social_read` | Unpin a message in a channel. |
| `social.entities.expand` | `social_read` | Batch-resolve channel/surrogate ids to metadata (max 100 ids). |
| `social.federation.add` | `social_read` | Add federated open-channel pointer on consumer project. |
| `social.federation.get` | `social_read` | List federation pointers for a consumer project. |
| `social.followers.list` | `social_read` | Users who follow the current user. |
| `social.friends.accept` | `social_read` | Accept friend request from user_id. |
| `social.friends.block` | `social_read` | Block a user. |
| `social.friends.cancel` | `social_read` | Cancel outgoing friend request. |
| `social.friends.cards` | `social_read` | Public card fields for each friend. |
| `social.friends.cards_batch` | `social_read` | Friend cards for specific user ids (must be friends). |
| `social.friends.list` | `social_read` | List friends for the current user. |
| `social.friends.reject` | `social_read` | Reject a pending friend request. |
| `social.friends.remove` | `social_read` | Remove an existing friend. |
| `social.friends.request` | `social_read` | Send a friend request (optional source context for public_channel). |
| `social.friends.request_respond` | `social_read` | Accept/reject/subscriber/block on an incoming friend request. |
| `social.friends.requests_in` | `social_read` | Incoming friend requests. |
| `social.friends.requests_out` | `social_read` | Outgoing friend requests. |
| `social.friends.user_ids` | `social_read` | Ordered friend user id list. |
| `social.merge_feed` | `social_read` | Merge read of multiple channel pointers (reader must access each). |
| `social.party_invite.mint` | `social_read` | Mint a short-lived party invite token for a relay room. |
| `social.party_invite.verify` | `social_read` | Verify party invite token (no auth required on REST; MCP still needs project context). |
| `social.pas.public_me_get` | `social_read` | Read Principal Access public slice for current user on a home project. |
| `social.pas.public_me_put` | `social_read` | Replace channels/chats/groups lists in PAS public slice. |
| `social.privacy.get` | `social_read` | Get friend/DM privacy settings. |
| `social.privacy.put` | `social_read` | Update privacy settings (incoming_friend_requests, dm_policy). |
| `social.public.index` | `social_read` | Read a page of the public discovery index. |
| `social.public.me` | `social_read` | Public maps from user row (ecosystem.social.public). |
| `social.public.publish` | `social_read` | Publish a channel/chats/groups entry to public index. |
| `social.public.quota_get` | `social_read` | Social public index quota for current user. |
| `social.public.reconcile` | `social_read` | Reconcile public maps (privileged role only). |
| `social.public.unpublish` | `social_read` | Remove a public index entry. |
| `social.support.assign` | `project_support` | Staff: assign a support ticket to a staff user id (or unassign with null). |
| `social.support.eligibility` | `project_support` | Batch read: for home project ids the user can access, returns whether end-user private support and/or public Messenger support lounge are enabled (channel id public support lounge channel for the project when public). |
| `social.support.history` | `project_support` | Read project support thread for the authenticated user (channel project support channel for the authenticated user). |
| `social.support.inbox` | `project_support` | Staff inbox preview list for a project (requires support access). |
| `social.support.request_human` | `project_support` | End user: request a human handoff for the current ticket (pending when allowed). |
| `social.support.search_projects` | `social_read` | Search projects the user may contact via support (titles ranked vs query). Returns support entry flags per row. |
| `social.support.send` | `project_support` | Send a message in the user's support thread for a project (user) or staff reply when permitted. |
| `social.support.transition` | `project_support` | Staff: transition a support ticket lifecycle status (requires support desk access). |
| `social.users.lookup` | `social_read` | Find users by id, email, or @username (no email in response). |
| `social.users.public_cards` | `social_read` | Public display cards for user ids (no friendship check). |

## storage (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `storage.admin_list_rows` | `—` | Ecosystem admin: list 8DNA rows with data.ecosystem.storage (project_id, dna_user_id, file counts, bytes). |
| `storage.admin_reconcile` | `—` | Ecosystem admin (platform hub): fix disk↔8DNA drift for any row. Tenants: use storage.reconcile with scope. |
| `storage.admin_scan` | `—` | Ecosystem admin (platform hub): compare on-disk files with 8DNA storage registry for (project_id, dna_user_id). Tenants: use storage.scan. |
| `storage.delete_file` | `—` | Deletes a file from ecosystem storage (JSON registry + blob). Requires write access for user scope; owner/admin for project scope. |
| `storage.get_hub_summary` | `—` | Hub v4 aggregate: permanent/temp totals, hosting bytes, pool cap, sites count. |
| `storage.get_project_usage_summary` | `—` | Owner/admin: total permanent/temp bytes across all members and project slice, plus project_pool_limit_bytes from the owner's subscription tier. |
| `storage.get_quota` | `—` | Returns storage quota for the current user or project slice: used/limit bytes, tier_basis (project_owner vs fallback), owner_user_id. Pass project_id or rely on API key / session project. scope=user ( |
| `storage.list_files` | `—` | Lists files in an ecosystem storage folder for the current user or project slice. Pass folder key (use '_' for root) or omit to list all folders (heavy). Set include_folders=1 to return folder_index f |
| `storage.reconcile` | `—` | Project RBAC: fix disk↔8DNA drift for the resolved row (dry_run default true). scope=user requires write; scope=project requires owner/admin. Prefer over storage.admin_reconcile for tenants. |
| `storage.scan` | `—` | Project RBAC: compare on-disk files with 8DNA storage registry for the resolved row. scope=user (default) scans caller member row; scope=project scans project slice (user_id=0). Prefer over storage.ad |

## system (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `system.ping` | `—` | Health check tool — always succeeds, no authentication required. |

## user (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `user.apikeys.create` | `—` | Create a personal AgentStack PAT on the signed-in user (ecosystem identity). |
| `user.apikeys.list` | `—` | List personal PATs (metadata only). Optional project_id filters agent keys scoped to that project. |
| `user.apikeys.revoke` | `—` | Revoke (soft-delete) a personal PAT by key_id. |
| `user.apikeys.rotate` | `—` | Rotate a personal PAT; returns a new secret once. |

## wallets (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `wallets.create` | `payments` | Create a wallet for a project or user. |
| `wallets.deposit` | `payments` | Deposit funds to a wallet. |
| `wallets.list` | `payments` | List wallets for a project (and optionally user). |
| `wallets.transfer` | `payments` | Transfer funds between wallets (same project). |

## web_push (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `web_push.get_health` | `—` | Per-user Web Push health (same payload as GET /api/push/health): VAPID, canDeliver, subscription/outbox counts, process-local worker stats. |

<!-- END:AUTOGEN-CAPABILITY-MATRIX -->

## References

- `docs/plugins/CONTEXT_FOR_AI.md` — intent routing by domain.
- `docs/adr/PUBLIC_DOCS_CLASSIFICATION.md` — audience policy.
