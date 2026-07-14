# AgentStack MCP Capability Matrix

> **Integrator reference.** Action IDs match `GET https://agentstack.tech/mcp/actions`. Regenerated with platform releases; do not edit descriptions by hand — run the monorepo redact step after refresh.


## admin (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `admin.data.dna_list` | `—` | Paginated DNA list with unified_8dna_role filters for admin Data hub. |
| `admin.data.health` | `—` | Cheap counts for platform admin data plane (projects/users DNA slices). |
| `admin.data.people` | `—` | Admin people bundle: projects (list shape) + users for a project. |

## agentnet (43)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agentnet.admin.grant_metrics` | `—` | Weekly grant metrics slice from ecosystem 8DNA (read-only). |
| `agentnet.admin.launch_readiness` | `—` | Grant Readiness Score (GRS v2), blockers, and submit_ready flag. |
| `agentnet.admin.schema_status` | `—` | Substrate M+ schema snapshot (required / deprecated / packed_in_8dna tiers). |
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

## agents (25)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agents.approve_run` | `agents_run` | Approve a run stuck in waiting_for_approval and resume execution. |
| `agents.create` | `agents_admin` | Create a new agent row (draft AgentSpec). |
| `agents.create_from_template` | `agents_admin` | Create an agent row from a built-in template (atomic merge on server). |
| `agents.delete` | `agents_admin` | Delete an agent row. |
| `agents.fork` | `agents_admin` | Fork an agent to a new generation (DNA row). |
| `agents.gates` | `agents_run` | Evaluate promotion gates for an agent. |
| `agents.get` | `agents_run` | Get a single agent by UUID (default owner=project; personal rows need owner=user). |
| `agents.kill` | `agents_admin` | Set agent lifecycle state to killed (hard stop for new runs). |
| `agents.list` | `agents_run` | List Agents Fleet rows for a project (8DNA entity_type=agent). |
| `agents.list_pending_approvals` | `—` | List runs waiting for approval across project or personal agent scope. |
| `agents.metrics` | `agents_run` | Read rollup metrics for an agent (ecosystem.agents_metrics). |
| `agents.policy_preview` | `agents_run` | Expand AgentPolicy patterns to live MCP actions (preview, no persistence). |
| `agents.promote` | `agents_admin` | Run auto-promotion gates; may set state to live when metrics pass. |
| `agents.rollout_advance` | `agents_admin` | Advance canary rollout step (5→20→50→100). |
| `agents.run` | `agents_run` | Enqueue an agent run (durable work queue). |
| `agents.run_get` | `agents_run` | Get a run row plus normalized RunDetailDTO for cockpit/audit views. |
| `agents.run_with_agnt_credits` | `agents_run` | Demo orchestration: purchase compute credits (AGNT) then enqueue an agent run. |
| `agents.runs_list` | `agents_run` | List agent runs with REST/SDK filters and lightweight audit fields. |
| `agents.stop` | `agents_run` | Cancel a running / queued agent run. |
| `agents.template_preview` | `agents_run` | Return merged AgentSpec-shaped preview for a template (no persistence). |
| `agents.templates_list` | `agents_run` | List built-in Agent Fleet templates (canonical Python catalog). |
| `agents.timeline` | `agents_run` | List recent runs for an agent (uuid + status + timestamps). |
| `agents.traces` | `agents_run` | Return stored run events (trace buffer) for a run. |
| `agents.update` | `agents_admin` | Replace AgentSpec for an agent (full document). |
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
| `apikeys.create` | `api_keys` | Create a new API key for project access. |
| `apikeys.delete` | `api_keys` | Delete an API key permanently. |
| `apikeys.list` | `api_keys` | List all API keys for a project. |

## assets (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `assets.create` | `assets_write` | Create a new asset for the project. |
| `assets.delete` | `assets_write` | Delete an asset from the project. |
| `assets.get` | `—` | Get asset details by ID. |
| `assets.list` | `—` | List all assets in the project. |
| `assets.list_presets` | `—` | List deterministic asset wizard presets for the project. |
| `assets.update` | `assets_write` | Update an existing asset. |

## auth (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `auth.get_profile` | `—` | Get user profile information. |
| `auth.login` | `—` | Login to the system. |
| `auth.register` | `—` | Register a new user. |
| `auth.update_profile` | `—` | Update user profile. |

## bots (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `bots.archive` | `—` | MCP tool bots.archive |
| `bots.attach_channel` | `—` | MCP tool bots.attach_channel |
| `bots.broadcast` | `—` | MCP tool bots.broadcast |
| `bots.conversations` | `—` | MCP tool bots.conversations |
| `bots.create` | `—` | MCP tool bots.create |
| `bots.get` | `—` | MCP tool bots.get |
| `bots.go_live` | `—` | MCP tool bots.go_live |
| `bots.health` | `—` | MCP tool bots.health |
| `bots.list` | `—` | MCP tool bots.list |
| `bots.pause` | `—` | MCP tool bots.pause |
| `bots.send_commerce_offer` | `—` | MCP tool bots.send_commerce_offer |
| `bots.set_brain` | `—` | MCP tool bots.set_brain |
| `bots.set_handoff` | `—` | MCP tool bots.set_handoff |
| `bots.simulate` | `—` | MCP tool bots.simulate |
| `bots.staff_reply` | `—` | MCP tool bots.staff_reply |
| `bots.templates` | `—` | MCP tool bots.templates |
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

## commerce (18)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commerce.ai.generate_products` | `—` | Dry-run AI product generation — returns wizard answer dicts only. Mirrors POST /api/commerce/ai/generate-products. |
| `commerce.ai.magic_fill` | `—` | Parse text into product wizard answers. Mirrors POST /api/commerce/ai/magic-fill. |
| `commerce.coupon.create` | `—` | Create a shop promo code (percent or fixed USDT off). Optional listing_uuids scopes the code to specific listings. Mirrors POST /api/commerce/merchant/coupons. |
| `commerce.coupon.delete` | `—` | Delete a project promo code. Mirrors DELETE /api/commerce/merchant/coupons/{code}. |
| `commerce.coupon.list` | `—` | List project-scoped promo codes from 8DNA commerce.coupon_registry. Mirrors GET /api/commerce/merchant/coupons. |
| `commerce.coupon.update` | `—` | Update a project promo code. Mirrors PUT /api/commerce/merchant/coupons/{code}. |
| `commerce.reconcile` | `—` | Ops: find marketplace deals stuck after confirm without transfer. Example: {"dry_run": true, "project_id": 1} |
| `commerce.refund.manual_complete` | `—` | Ecosystem admin: mark fiat/manual refund compensated — revoke entitlements, set order refunded. Mirrors POST /api/admin/commerce/manual-refunds/complete. |
| `commerce.refund.manual_list` | `—` | Ecosystem admin: list refund_requested orders awaiting manual compensation for a buyer commerce slice. Mirrors GET /api/admin/commerce/manual-refunds. |
| `commerce.refund.status` | `—` | Poll refund request + compensation for an order. Mirrors GET /api/commerce/orders/{order_id}/refund-request. |
| `commerce.sell.activate` | `—` | One-shot seller activation: earnings wallet, product seed, public policy, storefront index upsert, optional hosted vitrine. Mirrors POST /api/commerce/sell/activate. |
| `commerce.storefront.hosted_manifest` | `—` | Read hosted vitrine manifest (bucket, hosted_dirty, merchandising). Mirrors GET /api/commerce/storefront/hosted/manifest. |
| `commerce.storefront.hosted_publish` | `—` | Publish hosted vitrine static bundle + tenant boot JSON when dist is on server. Mirrors POST /api/commerce/storefront/hosted/publish. |
| `commerce.storefront.one_click_fill` | `—` | Create listings for eligible catalog assets without active listings. Mirrors POST /api/commerce/storefront/seed/one-click-fill. |
| `commerce.storefront.seed_apply` | `—` | Apply a Storefront Studio seed plan (asset upsert → listing → index). Mirrors POST /api/commerce/storefront/seed/apply. |
| `commerce.storefront.seed_ingest` | `—` | Parse bulk source (csv/json/ai_batch/magic/clone) into ProductSpec rows. Mirrors POST /api/commerce/storefront/seed/ingest. |
| `commerce.storefront.seed_plan` | `—` | Dry-run Storefront Studio seed — compose asset/listing drafts and guidance hints. Mirrors POST /api/commerce/storefront/seed/plan. |
| `commerce.storefront.seed_undo` | `—` | Undo a prior seed run — cancel listings and remove from storefront index. Mirrors POST /api/commerce/storefront/seed/undo. |

## commerce_rest (39)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rest.admin.commerce.manual_refunds_complete` | `—` | Ops: mark fiat/manual refund compensated — revoke entitlements, set order refunded. |
| `rest.admin.commerce.manual_refunds_list` | `—` | Ops: list refund_requested orders awaiting manual compensation for a buyer slice. |
| `rest.admin.commerce.metrics_scrape` | `—` | Prometheus scrape for commerce funnel gauges (header X-Metrics-Token, env COMMERCE_METRICS_SCRAPE_TOKEN). |
| `rest.admin.commerce.refund_sse_health` | `—` | Ops: in-process refund SSE subscriber counts on this worker (connection_count, pg_bus_enabled). |
| `rest.admin.commerce.settlement_funnel` | `—` | Ops: stuck escrow scan + checkout funnel OpTrace + refund_sse_health snapshot. |
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
| `rest.commerce.reconcile_escrow` | `—` | Ops: find deals confirmed without completed transfer (nightly gate). |
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
| `context.get` | `rag.read` | Unified Context Fabric gather for agents, logic, and MCP callers. |

## crm (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `crm.create_deal` | `—` | Create a CRM deal in the default pipeline. |
| `crm.erase_contact` | `—` | Anonymize/erase a CRM contact (GDPR). |
| `crm.export_contact` | `—` | Export GDPR bundle for a CRM contact. |
| `crm.get_contact_360` | `—` | Contact 360 view: profile, deals, unified timeline. |
| `crm.get_crm_config` | `—` | Project CRM config (saved_views, settings blob). |
| `crm.get_deal_timeline` | `—` | Activity timeline for a CRM deal (notes, tasks linked to deal_id). |
| `crm.import_contacts` | `—` | Batch import CRM contacts with email dedupe. |
| `crm.list_board` | `—` | Pipeline kanban board with stage columns and deals. |
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

## discovery (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `discovery.get_platform_surfaces` | `—` | List canonical platform routes from generated platform-surface-audit.json. |
| `discovery.list` | `—` | List all MCP actions (alias for GET /mcp/actions catalog). |

## finance (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `finance.activity` | `payments|agentcoin` | Unified wallet activity feed. |
| `finance.dashboard_bundle` | `payments|agentcoin` | Finance hub landing aggregate (portfolio + billing + activity preview). |
| `finance.portfolio` | `payments|agentcoin` | User finance portfolio aggregate (three rails + JSON wallets). |
| `finance.project.contribute` | `payments|project_admin` | Member contribution: USD only from personal ecosystem wallet (project_id=1) to project treasury. Not for custom/game currencies. |
| `finance.project.contributions.list` | `payments|agentcoin` | List member contributions for a project (FAP-masked). |
| `finance.project.fund` | `payments|project_admin` | Fund a project via ecosystem USD transfer. |
| `finance.project.portfolio` | `payments|agentcoin` | Project business portfolio (operating + treasury + AGNT). |

## fundraising (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `fundraising.narrative.lint` | `—` | Scan text for forbidden fundraising phrases from NARRATIVE_REGISTRY.yaml. |
| `fundraising.packet.compose` | `—` | List attachment paths for a grant program packet (registry + evidence). |

## generation (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `generation.approve` | `—` | Mark manual generation approval on anchor and re-run gate evaluation. |
| `generation.diff` | `—` | Structured diff between two resolved generation entity states (same table/project). |
| `generation.gates` | `—` | Run generation gate chain for an environment (schema, conflict, health, soak, metrics, optional manual). |
| `generation.list` | `—` | List sandbox environment anchors for a project (names, status, CalVer, auto_created flags). |
| `generation.promote` | `—` | Promote a sandbox environment to production (runs promotion checks first). |
| `generation.timeline` | `—` | Recent completed promotion requests for a project (newest first). |

## guidance (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `guidance.funnel_stats` | `—` | O(1) in-process Compass funnel counters for messaging-channel-bot: path_started, per-task completions, channel mix, dual_channel_attach. No database scan. |
| `guidance.list_capability_tasks` | `—` | List Platform Task Capability (PTC) atoms from platform fixture catalog Use before Compass playbooks with kind=capability or agentstack.execute task hints. |
| `guidance.match_playbook` | `—` | Match natural-language goal text to a Platform Compass playbook id using bundled intent patterns + RU/EN synonyms. Returns verifyKinds for bot/commerce paths. |
| `guidance.path_status` | `—` | Snapshot Compass verify gates for a project: botExists, botLive, botChannelAttached, hostedVitrinePublished. Use after playbook steps or before go-live checks. |
| `guidance.start_session` | `—` | Start a Platform Compass guidance session on the server (8DNA data.guidance). Use with messaging-channel-bot for cross-device resume. |

## hosting (18)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `hosting.bucket.file.get` | `—` | Read a single bucket file (text or base64, max 2MB). |
| `hosting.bucket.file.rename` | `—` | Rename or move a file within a bucket. |
| `hosting.bucket.files.bulk_delete` | `—` | Delete multiple files by path list or prefix. |
| `hosting.bucket.files.list` | `—` | List files in a hosting bucket (optional prefix, cursor pagination). |
| `hosting.demo.status` | `—` | Public demo hosting pool status (enabled, depth, sandbox project id). |
| `hosting.demo_store.status` | `—` | Golden promo demo-store vitrine: catalog row count on sandbox, published build id, manifest bucket (`frontend.commerce.hosted_storefront.gen1`). |
| `hosting.deploy_files` | `—` | Batch-upload files to a bucket and optionally publish (max 50 files per call). |
| `hosting.files.put` | `—` | Upload or replace a file in a hosting bucket. |
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

## integrations (48)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `integrations.activate_webhook` | `—` | Register provider inbound webhook for a connection (post-OAuth or manual retry). |
| `integrations.connection_health` | `mcp_read` | Read connection health snapshot (public spec only). |
| `integrations.connector_schema` | `mcp_read` | Provider setup schema metadata for wizard/agents. |
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
| `integrations.get_platform_diagnostics` | `—` | Cross-tenant Integration Hub rollup (platform admin ops). |
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
| `integrations.test_hook` | `mcp_read` | Dry-run inbound hook verify + normalize (sync dispatch to logic). |
| `integrations.test_scenario_step` | `—` | Dry-run a scenario step via logic simulation. |
| `integrations.update_connection` | `—` | Patch integration connection metadata (label, status, config, direction). |

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

## notifications (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `notifications.cancel_push` | `—` | Cancel pending push items by correlation key prefix. |
| `notifications.list_prefs` | `—` | Messenger + web push preference snapshot (minimal). |
| `notifications.register_category` | `—` | Register a logical push category in user messenger prefs (persisted). |
| `notifications.send` | `—` | Deprecated alias — use notifications.send_push or integrations webhooks. |
| `notifications.send_push` | `—` | Enqueue OS web push for a user (self or admin). |
| `notifications.subscribe_push` | `—` | Returns VAPID status; browser must still call PushManager.subscribe. |
| `notifications.update_prefs` | `—` | Patch messenger prefs (subset allowed by MessengerPrefsPatch). |

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

## projects (14)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `projects.add_user` | `project_admin` | Add a user to a project (requires Professional subscription or ecosystem context and manage_users/owner). |
| `projects.create_project` | `—` | Create a new project. |
| `projects.create_project_anonymous` | `—` | Create a new anonymous project (for AI agents). |
| `projects.delete_project` | `project_admin` | Delete a project (PSDP). |
| `projects.execute_deletion` | `project_admin` | Execute scheduled or immediate deletion with execution token. |
| `projects.get_deletion_inventory` | `—` | Pre-delete inventory: blockers, warnings, and execution_token for safe delete. |
| `projects.get_project` | `—` | Get detailed information about a specific project. |
| `projects.get_projects` | `—` | Get list of projects for the current user. |
| `projects.get_stats` | `—` | Get project statistics. |
| `projects.get_users` | `—` | Get list of users in a project. |
| `projects.remove_user` | `project_admin` | Remove a user from a project (requires Professional subscription or ecosystem context). |
| `projects.schedule_deletion` | `project_admin` | Schedule project deletion after inventory + execution token. |
| `projects.update_project` | `project_admin` | Update an existing project. |
| `projects.update_user_role` | `project_admin` | Update a user's role in a project (requires manage_users or owner; cannot assign owner via this tool). |

## rag (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rag.collection_create` | `rag.write` | Create a new RAG knowledge base collection for the current project. |
| `rag.collection_delete` | `rag.write` | Delete a RAG collection and all its documents. This is irreversible. |
| `rag.collection_list` | `rag.read` | List all RAG collections for the current project. |
| `rag.document_add` | `rag.write` | Add a text document to a RAG collection. |
| `rag.document_delete` | `rag.write` | Remove a document (all its chunks) from a collection by source_doc_id. |
| `rag.document_list` | `rag.read` | List document chunks stored in a collection. |
| `rag.memory_add` | `rag.write` | Store a conversation turn in AI memory. |
| `rag.memory_get` | `rag.read` | Get the most recent conversation turns from memory (chronological order). |
| `rag.memory_search` | `rag.read` | Semantically search past conversation turns. |
| `rag.search` | `rag.read` | Semantic search over a RAG collection. |

## rbac (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rbac.assign_role` | `—` | Assign a role to a user in a project. Requires manage_users or owner. Cannot assign owner via this tool. |
| `rbac.check_permission` | `—` | Check if a user has a permission in a project. If user_id is omitted, checks current user. |
| `rbac.get_roles` | `—` | Get list of roles for a project (system + custom). Returns id, name, permissions_bitmap, permissions, is_system. |
| `rbac.revoke_role` | `—` | Revoke a role from a user in a project. User will get default viewer role. Requires manage_users or owner. Cannot revoke owner. |

## scheduler (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `scheduler.cancel_task` | `scheduler` | Cancel/remove a scheduler task (removes from pool and disables in database). |
| `scheduler.clear_pool_tasks` | `scheduler` | Clear all tasks from the execution pool (does not delete from database). |
| `scheduler.create_task` | `scheduler` | Create a new scheduled task for automated execution. |
| `scheduler.delete_pool_task` | `scheduler` | Remove a specific task from the execution pool (does not delete from database). |
| `scheduler.execute_task` | `scheduler` | Execute a scheduled task immediately, bypassing the cron schedule. |
| `scheduler.get_all_db_tasks` | `scheduler` | Get all scheduler and standalone tasks from the database (heavy operation). |
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

## social (83)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `social.admin.channel_search` | `social_read` | Operator: search channel metadata on a home_project_id by substring (id/title). |
| `social.admin.chat_index_page` | `social_read` | Operator: paginated messenger chat index for a user (cursor/limit). |
| `social.admin.chat_sweep` | `social_read` | Run one chat history prune batch. Requires ecosystem owner/admin on project 1. |
| `social.admin.directory_search` | `social_read` | Operator: unified typeahead — users (8DNA) + channels for a home_project_id. |
| `social.admin.dm_thread_meta` | `social_read` | Operator: DM thread metadata (counts, participants) for support; audited. |
| `social.admin.force_close_channel` | `social_read` | Operator: delete channel metadata from project data_channels (ecosystem owner). |
| `social.admin.force_delete_message` | `social_read` | Operator: delete a chat message by id from DNA ring (ecosystem owner). Use dry_run first. |
| `social.admin.graph_edges` | `social_read` | Operator: list friend and blocked edges for a user (capped). |
| `social.admin.messenger_client_cache_get` | `social_read` | Operator: read messenger client cache limits (defaults, stored overrides, merged effective). |
| `social.admin.messenger_client_cache_set` | `social_read` | Operator: replace data.ecosystem.messenger_client_cache on ecosystem project (media/IDB/OPFS caps). |
| `social.admin.messenger_policy_get` | `social_read` | Operator: read messenger retention defaults, stored overrides, and merged effective policy. |
| `social.admin.messenger_policy_set` | `social_read` | Operator: replace messenger_operator_policy on ecosystem project (tiers, temp TTL, sweep ceiling). |
| `social.admin.public_index_status` | `social_read` | Operator: public index page or reconcile maps (set reconcile=true). |
| `social.admin.relay_hints` | `social_read` | Operator: static hints for stream relay room naming and diagnostics paths. |
| `social.admin.reset_messenger_prefs` | `social_read` | Operator: clear messenger_prefs for a user (reason required). |
| `social.admin.revoke_federation` | `social_read` | Operator: remove a federation pointer from consumer to source channel. |
| `social.admin.user_summary` | `social_read` | Operator: aggregate friend/request/block counts and messenger prefs summary for a user_id. |
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
| `social.chat.index_put` | `social_read` | Merge chat index entries into the user's sidebar index (no If-Match; use social flows for removal). |
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

## storage (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `storage.delete_file` | `—` | Deletes a file from ecosystem storage (JSON registry + blob). Requires write access for user scope; owner/admin for project scope. |
| `storage.get_hub_summary` | `—` | Hub v4 aggregate: permanent/temp totals, hosting bytes, pool cap, sites count. |
| `storage.get_project_usage_summary` | `—` | Owner/admin: total permanent/temp bytes across all members and project slice, plus project_pool_limit_bytes from the owner's subscription tier. |
| `storage.get_quota` | `—` | Returns storage quota for the current user or project slice: used/limit bytes, tier_basis (project_owner vs fallback), owner_user_id. Pass project_id or rely on API key / session project. scope=user ( |
| `storage.list_files` | `—` | Lists files in an ecosystem storage folder for the current user or project slice. Pass folder key (use '_' for root) or omit to list all folders (heavy). Set include_folders=1 to return folder_index f |

## system (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `system.ping` | `—` | Health check tool — always succeeds, no authentication required. |

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

## Why auto-generated?

Skills and commands must not duplicate action lists. Drift between docs and code (a real bug class of the gen1 plugin layout) is eliminated by regenerating this file from `GET /mcp/actions` on every release.

## References

- `docs/plugins/CONTEXT_FOR_AI.md` — intent routing by domain.
- `docs/API_KEY_SERVICE_CAPS.md` — cap catalog.
- `provided_plugins/cursor-plugin/skills/` — decision-first skills.
