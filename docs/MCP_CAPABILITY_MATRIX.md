# AgentStack MCP Capability Matrix

> **Integrator reference.** Action IDs match `GET https://agentstack.tech/mcp/actions`. Tenant-facing catalog only; platform-operator actions are omitted.

- Source: in-process `mcp.routes._build_mcp_actions_catalog_payload`
- Generated: 2026-10-08 18:17 UTC
- Audience: **public (tenant only)**
- Total actions: **726**
- Gene: `repo.plugins.capability_routing.gen1` · `docs.public.classification.gen1`

<!-- BEGIN:AUTOGEN-CAPABILITY-MATRIX -->

## agentnet (40)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agentnet.arb.proof_bundle_for_run` | `agentcoin` | Build ERC-8004 proof-to-task bundle for a Fleet run (Arbitrum narrative). |
| `agentnet.arb.status` | `agentcoin` | Arbitrum Sepolia rail status (RPC ping, registries). |
| `agentnet.balance` | `agentcoin` | Read AGNT ledger balance from the L0 PostgreSQL ledger for a project slice. |
| `agentnet.batch_proof` | `agentcoin` | Merkle inclusion paths for a posted AGNT batch (read-only). |
| `agentnet.bnb.apex_jobs_list` | `agentcoin` | List agent runs with completed BNB APEX jobs (L0 events). |
| `agentnet.bnb.proof_bundle_for_run` | `agentcoin` | L0 proof-to-task bundle for an agent run (read-only). |
| `agentnet.bnb.register_identity` | `agentcoin` | Register Fleet agent on BSC ERC-8004 via BNBAgent SDK. |
| `agentnet.bnb.status` | `agentcoin` | BSC testnet rail status (RPC ping, registries). |
| `agentnet.bridge.create_intent` | `agentcoin_post` | Create bridge intent (in\|out). REST: POST /api/agentnet/{project_id}/bridge/intents |
| `agentnet.bridge.events` | `agentcoin` | List normalized bridge chain events (relay idempotency log). |
| `agentnet.bridge.intents` | `agentcoin` | List bridge settlement intents for a project slice. |
| `agentnet.bridge.supply_snapshot` | `agentcoin` | L0 AGNT balance sum + in-flight bridge intents + optional EVM ``totalSupply``. |
| `agentnet.chain.finality_probe` | `agentcoin` | Read-only finality label probe (Solana adapter stub + Base chain id). |
| `agentnet.chain.intent_status` | `agentcoin` | Get chain intent status by intent_id. |
| `agentnet.chain.rails` | `agentcoin` | List enabled chain control rails (CCM). |
| `agentnet.chain.submit_intent` | `agentcoin` | Submit a chain control intent (Fleet run by default). |
| `agentnet.checkpoint.by_epoch` | `agentcoin` | Fetch one checkpoint row by epoch (includes ``batch_roots_json`` when stored). |
| `agentnet.checkpoint.covering_batch` | `agentcoin` | Resolve the newest checkpoint epoch that covers a ledger ``batch_id`` (read-only). |
| `agentnet.checkpoint.list_recent` | `agentcoin` | List recent AGNT checkpoint epochs for a project (newest first; no ``batch_roots_json``). |
| `agentnet.compute_credits.purchase` | `agentcoin_post|project_admin` | Purchase compute credits: AGNT user→treasury batch + idempotent ``builder_energy`` top-up. |
| `agentnet.compute_credits.quote` | `agentcoin` | Deterministic quote for AGNT cost of compute credits (demo v1 rate table). |
| `agentnet.evidence_receipt` | `agentcoin` | Build a self-contained evidence receipt (batch proof + checkpoint prefix witness). |
| `agentnet.funding.offer` | `agentcoin_post` | Get funding offer after R1 USDT payment (opt-in R2 vault). REST: GET funding/offer |
| `agentnet.funding.start_vault_deposit` | `agentcoin_post` | Start opt-in vault deposit intent linked to payment. |
| `agentnet.genome.get` | `agentcoin` | Read L0 genome lineage entry for an entity (ecosystem.genome_lineage). |
| `agentnet.genome.verify` | `agentcoin` | Verify commit_hash matches canonical GenomeTag JSON. |
| `agentnet.post_batch` | `agentcoin_post|project_admin` | Post a double-entry AGNT batch (idempotent per ``idempotency_key``). |
| `agentnet.proof_to_task` | `agentcoin` | Build ERC-8004 proof-to-task bundle; optional on-chain validation submit. |
| `agentnet.ptr.list_rails` | `agentcoin` | List enabled PTR rails (Base, BNB, Arb, Solana) with strategies. |
| `agentnet.receipt.verify` | `agentcoin` | Verify an AGNT receipt JSON (canonical hash + optional Merkle proof + optional Ed25519). |
| `agentnet.solana.proof_bundle_for_run` | `agentcoin` | Build proof-to-task v2 bundle for a Fleet run (Solana PTR). |
| `agentnet.solana.register_identity` | `agentcoin` | Register Fleet agent metadata on Solana attestation program (devnet). |
| `agentnet.solana.status` | `agentcoin` | Solana devnet PTR attestation rail status. |
| `agentnet.solana.submit_intent` | `agentcoin` | Submit Solana chain control intent (CCM) via gateway. |
| `agentnet.solana.submit_validation` | `agentcoin` | Submit task validation to Solana attestation program (best-effort). |
| `agentnet.testnet.faucet_mint` | `agentcoin_post` | Mint AGNT on L0 via testnet faucet (rate-limited). |
| `agentnet.testnet.list_profiles` | `agentcoin` | List AgentNet testnet chain profiles (lanes, faucet policy). |
| `agentnet.testnet.run_scenario` | `agentcoin` | Run a registered testnet scenario (grant demo, bridge smoke, etc.). |
| `agentnet.vault.deposit_confirm` | `agentcoin` | Credit L0 agUSD shares after ERC-4626 deposit (operator/indexer). |
| `agentnet.vault.nav` | `agentcoin` | ERC-4626 vault NAV (totalAssets, totalSupply). REST: GET /api/agentnet/{project_id}/vault/nav |

## agents (57)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agents.approve_run` | `agents_run` | Approve a run stuck in waiting_for_approval and resume execution. |
| `agents.automation_map` | `mcp_read` | Fleet automation aggregate: triggers, Logic rule ids, support ownership. |
| `agents.create` | `agents_admin` | Create a new agent row (draft AgentSpec). |
| `agents.create_from_template` | `agents_admin` | Create an agent row from a built-in template (atomic merge on server). |
| `agents.delete` | `agents_admin` | Delete an agent row. |
| `agents.ensure_agent` | `agents_run` | Resolve or provision a plan-node executor. Read-only by default (effect.kind=read); create=true provisions from top compatible template with bind_node=False, validates eligibility, then returns next_a |
| `agents.fleet_diagnostics` | `agents_run` | Project fleet diagnostics: runs, queue, orchestrator agent binding. |
| `agents.fork` | `agents_admin` | Fork an agent to a new generation (DNA row). |
| `agents.gates` | `agents_run` | Evaluate promotion gates for an agent. |
| `agents.get` | `agents_run` | Get a single agent by UUID (default owner=project; personal rows need owner=user). |
| `agents.health` | `mcp_read` | Fleet ECS index health for a project (agents/bots organelle diagnostics). |
| `agents.import_from_asset` | `agents_admin` | Create agent from commerce asset extensions.agent_fleet preset. |
| `agents.instruction_rules_list` | `mcp_read` | List merged platform instruction rules (fixture + ecosystem overlay). |
| `agents.instruction_rules_upsert` | `project_admin` | Upsert one instruction rule on ecosystem project overlay (8DNA leaf). |
| `agents.kill` | `agents_admin` | Set agent lifecycle state to killed (hard stop for new runs). |
| `agents.list` | `agents_run` | List Agents Fleet rows. Default projection=summary reads the fleet index card (uuid, title, status), not agent_spec. projection=full loads the agent row. |
| `agents.list_pending_approvals` | `mcp_read` | List runs waiting for approval across project or personal agent scope. |
| `agents.metrics` | `agents_run` | Read rollup metrics for an agent (ecosystem.agents_metrics). |
| `agents.orchestrate` | `mcp_read` | Run the project AI orchestrator for a channel (workspace\|messenger\|bot\|mcp\|api). |
| `agents.plan_apply_affinities` | `project_admin` | Apply affinity hints and optionally pin skill_ids/gene_tags to plan DNA. |
| `agents.plan_apply_proposal` | `project_admin` | Apply a planner proposal to the Work Graph with CAS (if_match_revision). Pass proposal from plan_propose and base_graph_revision unchanged. |
| `agents.plan_claim` | `mcp_read` | Claim pending plan leaves as in_progress for one agent (parallel work, WIP capped). Server-managed on claim: status, agent_id, meta.claimed_at\|lease_until\|heartbeat_at. Preserves title/objective/predi |
| `agents.plan_execute` | `project_admin` | Run orchestrator focused on current plan graph (alias for orchestrate with plan focus). Returns plan_revision / completion decision when available. |
| `agents.plan_get` | `mcp_read` | Read project Work Graph / plan (8DNA ecosystem.orchestrator.task_list) with metrics. Server-managed / read-only: root_run_id, claim lease fields (meta.claimed_at\|lease_until\|heartbeat_at\|last_claim), |
| `agents.plan_ingest` | `mcp_read` | Append diagnostic or work-intake findings as plan/Work Graph tasks. intake_mode=diagnostic (default) or work. Existing claimed nodes are left alone; matching issues bump occurrence_count. |
| `agents.plan_patch` | `project_admin` | Merge plan graph nodes (deep by id). Pass if_match_revision from plan_get for CAS (fleet/autonomous writers must send it). F1: when auto_generation_mode is on, pass env_uuid from sandbox fork; diff pa |
| `agents.plan_preview_affinities` | `mcp_read` | Preview suggested gene/skill hints on plan nodes (read-only, no DNA pin). |
| `agents.plan_process_backlog` | `mcp_read` | Scan decomposition backlog (decomposition_status=needed, no execution decomposition) and build planner proposals. Claims planner-parent lease before decompose when required_role=planner (even when aut |
| `agents.plan_propose` | `mcp_read` | Build a validated planner proposal (draft) for create_plan or decompose_node. Does not mutate task_list — call agents.plan_apply_proposal to persist. |
| `agents.plan_reclaim_stale` | `mcp_read` | Return in_progress plan nodes with expired claim lease to ready (or pending if not eligible). Clears agent_id + lease meta; stamps meta.last_claim. Mutating DNA write — not read-only. |
| `agents.plan_reconcile_diagnostics` | `mcp_read` | Auto-complete open diagnostic plan nodes when live findings are cleared (statuses_toward_completed: typically pending→verifying→completed). |
| `agents.plan_recovery_scan` | `mcp_read` | Read-only scan for verification_failed repair candidates and stale claims. Reason codes use recovery_engine SoT (verification_failed, expired_claim). |
| `agents.plan_refresh_affinities` | `mcp_read` | Deprecated alias: use plan_preview_affinities or plan_apply_affinities. |
| `agents.plan_suggest_tree` | `mcp_read` | Suggest a nested plan tree draft from a goal (read-only). Does not mutate task_list — use plan_propose/create_plan + plan_apply_proposal to persist. |
| `agents.policy_preview` | `agents_run` | Expand AgentPolicy patterns to live MCP actions (preview, no persistence). |
| `agents.promote` | `agents_admin` | Run auto-promotion gates; may set state to live when metrics pass. |
| `agents.provider_preflight` | `mcp_read` | Read-only LLM provider snapshot for an agent before enqueue (quota hints, fallbacks). |
| `agents.reconcile_binding` | `mcp_read` | Validate and repair project orchestrator agent_uuid vs fleet index and lineage. |
| `agents.rollout_advance` | `agents_admin` | Advance canary rollout step (5→20→50→100). |
| `agents.run` | `agents_run` | Enqueue an agent run (durable work queue). |
| `agents.run_get` | `agents_run` | Get a run row plus normalized RunDetailDTO for cockpit/audit views. |
| `agents.run_resume_packet` | `mcp_read` | Cross-provider continuation: goal, step, packet, skills — no chat transcript. |
| `agents.run_with_agnt_credits` | `agents_run` | Demo orchestration: purchase compute credits (AGNT) then enqueue an agent run. |
| `agents.runs_list` | `agents_run` | Rows are uuid, status, event_count, and timestamps. Event bodies stay on agents.run_get. Status and the event count are read in SQL. |
| `agents.skills_get` | `project_admin` | Fetch one skill card with progressive disclosure (full text). |
| `agents.skills_list` | `project_admin` | List ecosystem skill cards (fixture + optional ecosystem overlay). |
| `agents.skills_preview` | `project_admin` | Short skill preview for instruction packet (title + excerpt). |
| `agents.stop` | `agents_run` | Cancel a running / queued agent run. |
| `agents.team.create` | `project_admin` | Thin wrapper: plan_ingest team template + optional orchestrate focus. |
| `agents.template_preview` | `agents_run` | Return merged AgentSpec-shaped preview for a template (no persistence). |
| `agents.templates_list` | `agents_run` | List built-in Agent Fleet templates (canonical Python catalog). |
| `agents.timeline` | `agents_run` | List recent runs for an agent (uuid + status + timestamps). |
| `agents.traces` | `agents_run` | Return stored run events (trace buffer) for a run. |
| `agents.update` | `agents_admin` | Update an agent. `section`+`section_value` or `path_updates` load the current AgentSpec, merge objects (lists replace), validate, then `update_agent_spec` (Logic sync for workflow, reactions, schedule |
| `agents.version_timeline` | `mcp_read` | List agent version lineage (generation tree — REST GET /timeline parity). |
| `agents.work_next` | `mcp_read` | Agent-native next-work packet. Default detail=compact: state, next_action, reason, revision. Pass detail=full for instruction_packet, blocked_work, and plan_summary. Read-only unless claim=true. Optio |
| `agents.work_status` | `mcp_read` | Plan summary only: task counts and execution-ready leaves. The packet is small; the server still reads the plan leaf to count it. No node pick. |

## ai_builder (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `ai_builder.build.enqueue` | `mcp_read` | Queue a Vite build. Returns immediately with build_uuid or status=cached. Does not call an LLM. |
| `ai_builder.build.status` | `mcp_read` | Read a Vite build status, including file and line when it failed. |
| `ai_builder.compose.apply` | `project_admin` | Materialize a blueprint or UAM onto the project source tree. Does not run Vite. |
| `ai_builder.compose.preview` | `mcp_read` | Deterministic compose preview: returns content_sha256 and file paths. |
| `ai_builder.manifest.get` | `mcp_read` | Load Unified Application Manifest (UAM v1) for the current project. |
| `ai_builder.manifest.validate` | `mcp_read` | Validate a UAM v1 JSON object (structure + catalog + logic templates). |
| `ai_builder.publish` | `project_admin` | Copy the dev Vite build into a hosting bucket as an SPA and publish it. |
| `ai_builder.source.get` | `mcp_read` | Read one UTF-8 source file and its sha256. |
| `ai_builder.source.list` | `mcp_read` | List React source files under the project App Studio tree (src/** and related). Not hosting /s/ bucket bytes. |
| `ai_builder.source.patch` | `project_admin` | Patch one source file by replacements, a whole function, full content, or a unified diff. Never enqueues a build. Do not patch the published assets/index-*.js bundle. |
| `ai_builder.source.put` | `mcp_read` | Create or replace one source file. Does not start Vite. |

## analytics (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `analytics.archive.query` | `analytics` | Read-only cold archive rows (audit_logs + storage quota). Not included in analytics.project_snapshot. |
| `analytics.bump` | `analytics` | Increment analytics day-ring counters (catalog keys only: ae, ee, pe, rc, ec, eo, ev.*). Prefer organic writers; use for tests or manual corrections. |
| `analytics.get_metrics` | `analytics` | Custom DNA metrics array (data.metrics[]) for a project — not performance KPIs. For dashboard KPIs use analytics.project_snapshot. |
| `analytics.get_usage` | `analytics` | Project activity usage slice (members + API/exec events). Prefer analytics.project_snapshot with include=['activity']. Date filters are accepted for forward-compat but not applied. |
| `analytics.project_snapshot` | `analytics` | Unified project analytics snapshot: activity, finance, CRM, product events, optional business/bots/hosting slices. Prefer this over analytics.get_usage. |

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
| `assets.get` | `mcp_read` | Get asset details by ID. |
| `assets.list` | `mcp_read` | List all assets in the project. |
| `assets.list_presets` | `mcp_read` | List deterministic asset wizard presets for the project. |
| `assets.update` | `assets_write` | Update an existing asset. |

## auth (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `auth.channel_identities.list` | `mcp_read` | List bot channel identities linked to the authenticated user profile. |
| `auth.channel_identities.revoke` | `project_admin` | Revoke a channel identity link by id (owner-only). |
| `auth.email_otp.login` | `—` | Verify the email code and mint a session. data.sessions[jti] survives a process restart. The SDK stores the bearer. Hosted flagship sends body.p. MFA: retry with mfa_ticket and mfa_code. |
| `auth.email_otp.send` | `—` | One-time sign-in code. purpose=login\|register\|convert. No auth.convert_anonymous_user tool. sent=true hides whether the account exists. needs_human: ask the human. UI: AuthWidget or <agentstack-auth>. |
| `auth.get_profile` | `—` | Get the session card for the current API key. |
| `auth.identity.conflicts` | `mcp_read` | List project member emails that diverge from ecosystem canonical auth email. |
| `auth.identity.resolve` | `mcp_read` | Resolve canonical vs display email for a user (support / debug). |
| `auth.login` | `—` | Email+password tenant sign-in — not Cursor MCP Connect. Passwordless: auth.email_otp.send → login. Session live: omit email → get_profile. |
| `auth.password_reset.request` | `—` | Request a self-service password reset email for an ecosystem user. Always returns the same success message (enumeration-safe). The user opens the link in the email, sets a new password, then signs in. |
| `auth.register` | `—` | New platform user (email+password). Passwordless: auth.email_otp.send purpose=register → login. Then auth.get_profile. |
| `auth.switch_project` | `—` | Mint a session for an accessible target project (Session OS switch contour). |
| `auth.update_profile` | `project_admin` | Update user profile. |

## bots (23)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `bots.archive` | `bots_admin` | MCP tool bots.archive |
| `bots.attach_channel` | `bots_admin` | Attach a project messaging connection (telegram, max, whatsapp, …) to a bot fleet row. |
| `bots.broadcast` | `bots_admin` | MCP tool bots.broadcast |
| `bots.commands.upsert` | `bots_admin` | Merge one or more bot commands by trigger. Never replaces the command list. Use this instead of bots.update when editing /start, /help, or a single handler. |
| `bots.conversations` | `bots_run` | List bot conversations with handoff/needs_reply enrichment for inbox UI. |
| `bots.create` | `bots_admin` | MCP tool bots.create |
| `bots.dlq_list` | `bots_run` | MCP tool bots.dlq_list |
| `bots.dlq_replay` | `bots_admin` | MCP tool bots.dlq_replay |
| `bots.ensure_mentor_commands` | `bots_admin` | Heal mentor /start+/menu+/operator commands + inline buttons (idempotent). |
| `bots.fleet_diagnostics` | `bots_run` | Project bots fleet health (channel health, sessions) — project read RBAC. |
| `bots.get` | `bots_run` | MCP tool bots.get |
| `bots.go_live` | `bots_admin` | Activate bot webhooks and set lifecycle live (Telegram / MAX / WhatsApp channels). |
| `bots.health` | `bots_run` | MCP tool bots.health |
| `bots.list` | `bots_run` | Default projection=summary rows are uuid, title, deployment_state, and record_status. projection=full is bot_spec. |
| `bots.pause` | `bots_admin` | MCP tool bots.pause |
| `bots.send_commerce_offer` | `bots_run` | Send a commerce offer card to a bot conversation (listing UUID → outbound attachment). |
| `bots.set_brain` | `bots_admin` | MCP tool bots.set_brain |
| `bots.set_handoff` | `bots_run` | MCP tool bots.set_handoff |
| `bots.simulate` | `bots_run` | Dry-run one bot brain turn without outbound. Heavy LLM: at most one per sync agentstack.execute (60s batch; 180s if this is the only heavy step). Suites: one simulate per execute, or options.async + d |
| `bots.staff_reply` | `bots_run` | MCP tool bots.staff_reply |
| `bots.templates` | `bots_run` | MCP tool bots.templates |
| `bots.update` | `bots_admin` | Partial bot_spec update (commands, brain, name, …) — same as REST PUT. commands is a keyed list: pass write_mode (merge\|replace\|delete). A short commands array without write_mode merges by trigger and |
| `bots.waba_templates` | `bots_run` | MCP tool bots.waba_templates |

## buffs (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `buffs.apply_buff` | `project_admin` | Apply a buff to an entity (user or project). |
| `buffs.apply_persistent_effect` | `project_admin` | Quickly apply a persistent effect (creates and applies permanent buff). |
| `buffs.apply_temporary_effect` | `project_admin` | Quickly apply a temporary effect (creates and applies buff in one step). |
| `buffs.cancel_buff` | `mcp_read` | Cancel a buff in any state (force cancellation). |
| `buffs.create_buff` | `project_admin` | Create a buff template in PENDING state. |
| `buffs.extend_buff` | `mcp_read` | Extend the duration of an active buff. |
| `buffs.get_buff` | `mcp_read` | Get information about a specific buff. |
| `buffs.get_effective_limits` | `mcp_read` | Get effective limits for an entity with all buffs applied. |
| `buffs.list_active_buffs` | `mcp_read` | List active buffs for an entity. |
| `buffs.revert_buff` | `mcp_read` | Revert an active buff - restore original state from snapshot. |

## business (13)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `business.apply_tariff` | `project_admin` | Apply a tariff template to a business head project. |
| `business.attach_child` | `project_admin` | Attach a child organ project to a business head. |
| `business.command_snapshot` | `mcp_read` | Business command center aggregate (finance + CRM + org). |
| `business.create_composite` | `project_admin` | Create business head + attach organ children from template. |
| `business.detach_child` | `project_admin` | Detach a child organ from business head. |
| `business.get_org` | `mcp_read` | Get business org config and children index for a head project. |
| `business.link_child` | `mcp_read` | Link an existing owned project as a business organ (adopt). |
| `business.list_adopt_candidates` | `mcp_read` | List projects eligible for adopt/link into a business head. |
| `business.list_children` | `mcp_read` | List child organ projects for a business head. |
| `business.list_composite_adopt_candidates` | `mcp_read` | List standalone projects eligible for adopt during composite business create. |
| `business.list_tariff_templates` | `mcp_read` | List business tariff templates available for a head project. |
| `business.patch_org_settings` | `project_admin` | Patch business head org settings (config.org leaf). |
| `business.transfer_preflight` | `project_admin` | Scan transfer blockers before listing a project for sale. |

## calendar (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `calendar.event.delete` | `project_admin` | Delete a manual calendar event by id. |
| `calendar.event.upsert` | `project_admin` | Create or update a manual calendar event on ecosystem.calendar.manual_events. |
| `calendar.events` | `mcp_read` | List calendar events merged from CRM deals, activities, invoices, manual. |

## cardgame (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `cardgame.dna.read_rows` | `mcp_read` | List up to ``limit`` rows for a cardgame 8DNA entity type using DNA CRUD get (same permission model as the SPA). Use for match room / cell snapshots when building agent context. |
| `cardgame.rules.execute` | `project_admin` | Execute a Logic Engine command for ArcaneStack (e.g. ``cardgame.match.play_card``) with the same payload shape as the game client. Requires authenticated MCP context. |

## channels (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `channels.delete_source` | `mcp_read` | Remove a notification source by id. |
| `channels.deliver` | `mcp_read` | Send via unified channel plane (same bus as notifications.deliver). |
| `channels.delivery_status` | `mcp_read` | Read delivery receipts for a correlation_key from user DNA. |
| `channels.enroll_source` | `mcp_read` | Upsert a notification source (bot channel, web_push, email enrollment). |
| `channels.flush_digest` | `mcp_read` | Flush pending quiet-hours / digest notifications for the signed-in user. |
| `channels.list_event_triggers` | `mcp_read` | Catalog of normalized channel neural_event kinds for Logic subscriptions. |
| `channels.list_failed_deliveries` | `mcp_read` | Recent failed/skipped delivery receipts for the user. |
| `channels.list_identities` | `mcp_read` | List linked bot channel identities for the signed-in user. |
| `channels.resolve_routing` | `mcp_read` | Preview effective channels after prefs ∩ sources ∩ project policy. Reads those prefs on the server and does not return the profile. |
| `channels.route` | `mcp_read` | List, put, or remove one channel binding for the signed-in user. op=list returns the stored rows and the email plan. |
| `channels.test_delivery` | `mcp_read` | Enqueue a short test message on one channel (self or admin). |

## checklist (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `checklist.append_item` | `mcp_read` | Append a checklist step. |
| `checklist.clear` | `mcp_read` | Clear all checklist items for an entity. |
| `checklist.get` | `mcp_read` | Get checklist for an entity (e.g. crm_deal). |
| `checklist.set` | `mcp_read` | Replace checklist items for an entity. |
| `checklist.toggle_item` | `mcp_read` | Toggle or set status on one checklist item. |

## commands (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commands.execute` | `logic_write|mcp.execute` | Execute a single Protein Command via universal API. |
| `commands.execute_batch` | `logic_write|mcp.execute` | Execute multiple Protein Commands in batch. |

## commerce (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commerce.ai.generate_products` | `payments` | Dry-run AI product generation — returns wizard answer dicts only. Mirrors POST /api/commerce/ai/generate-products. |
| `commerce.ai.magic_fill` | `payments` | Parse text into product wizard answers. Mirrors POST /api/commerce/ai/magic-fill. |
| `commerce.coupon.create` | `payments` | Create a shop promo code (percent or fixed USDT off). Optional listing_uuids scopes the code to specific listings. Mirrors POST /api/commerce/merchant/coupons. |
| `commerce.coupon.delete` | `payments` | Delete a project promo code. Mirrors DELETE /api/commerce/merchant/coupons/{code}. |
| `commerce.coupon.list` | `payments` | List project-scoped promo codes from 8DNA commerce.coupon_registry. Mirrors GET /api/commerce/merchant/coupons. |
| `commerce.coupon.update` | `payments` | Update a project promo code. Mirrors PUT /api/commerce/merchant/coupons/{code}. |
| `commerce.refund.manual_complete` | `payments` | Ecosystem admin: mark fiat/manual refund compensated — revoke entitlements, set order refunded. Mirrors POST /api/admin/commerce/manual-refunds/complete. |
| `commerce.refund.manual_list` | `payments` | Ecosystem admin: list refund_requested orders awaiting manual compensation for a buyer commerce slice. Mirrors GET /api/admin/commerce/manual-refunds. |
| `commerce.refund.status` | `payments` | Poll refund request + compensation for an order. Mirrors GET /api/commerce/orders/{order_id}/refund-request. |
| `commerce.sell.activate` | `payments|project_admin` | One-shot seller activation: earnings wallet, product seed, public policy, storefront index upsert, optional hosted vitrine. Mirrors POST /api/commerce/sell/activate. |
| `commerce.storefront.health` | `payments` | Storefront index + ECS organelle health (admin diagnostics slice). |
| `commerce.storefront.hosted_manifest` | `payments` | Read hosted vitrine manifest (bucket, hosted_dirty, merchandising). Mirrors GET /api/commerce/storefront/hosted/manifest. |
| `commerce.storefront.hosted_publish` | `payments` | Publish hosted vitrine static bundle + tenant boot JSON when dist is on server. Mirrors POST /api/commerce/storefront/hosted/publish. |
| `commerce.storefront.list` | `payments` | List storefront listings via StorefrontReadFacade (ECS organelle). Mirrors GET /api/commerce/storefront/listings. |
| `commerce.storefront.one_click_fill` | `payments` | Create listings for eligible catalog assets without active listings. Mirrors POST /api/commerce/storefront/seed/one-click-fill. |
| `commerce.storefront.seed_apply` | `payments` | Apply a Storefront Studio seed plan (asset upsert → listing → index). Mirrors POST /api/commerce/storefront/seed/apply. |
| `commerce.storefront.seed_ingest` | `payments` | Parse bulk source (csv/json/ai_batch/magic/clone) into ProductSpec rows. Mirrors POST /api/commerce/storefront/seed/ingest. |
| `commerce.storefront.seed_plan` | `payments` | Dry-run Storefront Studio seed — compose asset/listing drafts and guidance hints. Mirrors POST /api/commerce/storefront/seed/plan. |
| `commerce.storefront.seed_undo` | `payments` | Undo a prior seed run — cancel listings and remove from storefront index. Mirrors POST /api/commerce/storefront/seed/undo. |

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

## context (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `context.get` | `rag.read` | Prefetch RAG + memory context for a task. |
| `context.hint_use_params_project_id` | `mcp_read` | Instruction-only: there is no MCP context.set tool. Pass params.project_id on each execute step; use auth.switch_project to change JWT scope. |
| `context.resolve` | `mcp_read` | Resolve effective project_id for the session; surfaces ambiguous_project_context. |

## crm (21)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `crm.create_company` | `crm` | Create a CRM company for a project. |
| `crm.create_deal` | `crm` | Create a CRM deal in the default pipeline. |
| `crm.delete_deal` | `crm` | Soft-delete (archive) a CRM deal. |
| `crm.erase_contact` | `crm` | Anonymize/erase a CRM contact (GDPR). |
| `crm.export_contact` | `crm` | Export GDPR bundle for a CRM contact. |
| `crm.get_contact_360` | `crm` | Contact 360 view: profile, deals, unified timeline. |
| `crm.get_crm_config` | `crm` | Project CRM config (saved_views, settings blob). |
| `crm.get_deal_timeline` | `crm` | Activity timeline for a CRM deal (notes, tasks linked to deal_id). |
| `crm.import_contacts` | `crm` | Batch import CRM contacts with email dedupe. |
| `crm.list_board` | `crm` | Pipeline board. Default deals are cards: entity_id, title, status, updated_at. projection=full is the deal record. Stage columns stay. |
| `crm.list_companies` | `crm` | Default projection=summary rows are entity_id, kind, title, status, and updated_at. projection=full is the company record. |
| `crm.list_contacts` | `crm` | Default projection=summary rows are entity_id, kind, title, status, and updated_at. projection=full is the contact record. |
| `crm.log_activity` | `crm` | Log a CRM activity (note, call, task, etc.). |
| `crm.magic_fill` | `crm` | AI/heuristic autofill for quick-create (dry-run, no write). |
| `crm.merge_contacts` | `crm` | Merge duplicate contact into primary (absorb duplicate). |
| `crm.move_deal_stage` | `crm` | Move a deal to another pipeline stage. |
| `crm.patch_crm_config` | `crm` | Patch project CRM config (writers may update saved_views only). |
| `crm.search` | `crm` | Search CRM contacts. Default projection=summary is the card (entity_id, kind, title, status, updated_at). projection=full is the hit record. |
| `crm.suggest_field` | `crm` | Suggest values for a CRM field from partial context. |
| `crm.update_deal` | `crm` | Patch a CRM deal (title, amount, due_at, stage_id, contact_ids, custom). |
| `crm.upsert_contact` | `crm` | Create or update a CRM contact (dedupe by email). |

## data_access (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `data_access.apply_defaults_template` | `data_access_admin` | Copy missing `resources.<name>` entries from the global template into `target_project_id`'s field_access_policy. Does not overwrite existing keys. Cannot target ecosystem project id=1. |
| `data_access.check_field` | `mcp_read` | Check whether a specific role can read/write a field in a resource. |
| `data_access.get_defaults_template` | `mcp_read` | Read the global Field Access Policy template from ecosystem project DNA (`config.field_access_defaults`: default_access, globals, resources). |
| `data_access.get_policy` | `mcp_read` | Retrieve the current field-level access policy for a project. |
| `data_access.get_triggers` | `mcp_read` | Read FAP v1.2 ``field_triggers`` for a project (same JSON as ``field_access_policy.field_triggers``). Keyed by resource → pattern → list of trigger definitions. See FIELD_ACCESS_POLICY.md — Field Trig |
| `data_access.set_defaults_template` | `data_access_admin` | Write the global FAP template on ecosystem project (admin tooling). |
| `data_access.set_policy` | `data_access_admin` | Create or update the field-level access policy for a project resource. |
| `data_access.set_triggers` | `data_access_admin` | Upsert ``field_triggers`` for one resource. **triggers** is a map ``field_pattern → [trigger_def, …]`` (patterns like ``status``, ``config.**``, ``*``). |
| `data_access.test_mask` | `mcp_read` | Preview what fields a given role can read/write in a resource. |

## diagnostics (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `diagnostics.consistency.scan` | `admin.read` | Scan integrity; set sync_plan=true to append findings as plan tasks (default read-only). |
| `diagnostics.gene_token_query` | `admin.read` | GTPI bool query against process-local gene token lexicon. |
| `diagnostics.neural_graph` | `admin.read` | Neural Visualizer Gen2 snapshot: topology, heat planes (protein/api/dna_query/gene), composition edges, hot_region_hints. Always includes heat_meta (worker layout honesty). Process-local heat; Tier F |
| `diagnostics.promote_hot_gene` | `admin.write` | Promote a genetic tag's path prefixes into HotProteinRegion for an entity. Modes: warm, pin, prefetch_only. Tier F materialize blocked. |

## discovery (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `discovery.describe` | `—` | Full descriptor for one MCP action (schema + instruction_slice). |
| `discovery.domain` | `—` | List actions in one catalog domain (wrapper over discovery.search). |
| `discovery.get_platform_surfaces` | `—` | List canonical platform routes from generated platform-surface-audit.json. |
| `discovery.job_status` | `—` | Poll an async agentstack.execute job (options.async=true). Cheap. Use after a multi-step heavy batch; do not re-run bots.simulate to check status. |
| `discovery.list` | `—` | List all MCP actions (alias for GET /mcp/actions catalog). Includes execute_budget (sync 60s / one heavy LLM step) and write_modes (merge\|replace\|append\|delete — pass write_mode on collection/body wri |
| `discovery.recipes` | `—` | List onboarding MCP recipes (instruction plane SoT). |
| `discovery.search` | `—` | Compact catalog search — returns ≤50 matching actions (not full discovery.list). |
| `discovery.status` | `—` | Registry status — version, catalog etag, action/domain/recipe totals. |
| `discovery.summary` | `—` | Lightweight MCP catalog totals (alias for GET /mcp/actions/summary). |

## dna (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `dna.lineage.get_ancestors` | `8dna.read` | Walk ancestor chain for an 8DNA row (materialized path or scoped CTE fallback). |
| `dna.lineage.get_descendants` | `8dna.read` | List descendant rows under a parent UUID within project scope. |

## docs_nav (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `docs_nav.resolve` | `—` | Resolve a genetic navigation tag to AI_INDEX path(s) from TAG_CATALOG. For code/docs navigation only — for runtime MCP tools use discover/by_intent. |
| `docs_nav.search` | `—` | BM25-lite search over AI navigation catalog (map/index triggers). Returns genetic tags + AI_INDEX paths. Not for selecting runtime MCP tools. |

## energy (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `energy.get_balance` | `mcp_read` | LLM prepaid energy balance for user or project pool. |
| `energy.list_packs` | `mcp_read` | Available LLM energy pack tiers (small/medium/large) and USD prices. |
| `energy.purchase` | `project_admin` | Purchase energy pack via unified finance.pay (wallet) or payments.create (card). |

## finance (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `finance.activity` | `mcp_read` | Unified wallet activity feed. |
| `finance.dashboard_bundle` | `mcp_read` | Finance hub landing aggregate (portfolio + billing + activity preview). |
| `finance.expense.create` | `project_admin` | Create an expense row on data.money.expenses[]. |
| `finance.expense.delete` | `project_admin` | Delete expense by id. |
| `finance.expense.list` | `mcp_read` | List project expenses on the canonical money.expenses leaf. Not inside a generation sandbox. Filter by date_from/date_to then limit. |
| `finance.pay.execute` | `project_admin` | Execute a quoted pay intent with idempotency key. |
| `finance.pay.quote` | `mcp_read` | Quote a unified pay intent (energy_pack, subscription, compute_credits, fund, contribute). |
| `finance.portfolio` | `mcp_read` | User finance portfolio aggregate (three rails + JSON wallets). |
| `finance.project.analytics` | `mcp_read` | Project finance analytics (revenue/expense trends) for hosted vertical widgets. |
| `finance.project.contribute` | `payments|project_admin` | Member contribution: USD only from personal ecosystem wallet (project_id=1) to project treasury. Not for custom/game currencies. |
| `finance.project.contributions.list` | `mcp_read` | List member contributions for a project (FAP-masked). |
| `finance.project.fund` | `payments|project_admin` | Fund a project via ecosystem USD transfer. |
| `finance.project.portfolio` | `payments|agentcoin` | Project business portfolio (operating + treasury + AGNT). |
| `finance.revenue.delete` | `project_admin` | Delete a paid freelancer revenue row by id (actor-owned). |
| `finance.revenue.list` | `mcp_read` | List paid freelancer revenue rows on data.money.invoices[] (actor-scoped). |
| `finance.revenue.record` | `mcp_read` | Record paid freelancer revenue on data.money.invoices[] (EDITFLOW deal close). |
| `finance.revenue.update` | `project_admin` | Update paid freelancer revenue amount or description (actor-owned). |

## generation (20)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `generation.approve` | `project_admin` | Mark manual generation approval on anchor and re-run gate evaluation. |
| `generation.canary.abort` | `project_admin` | Abort canary rollout and restore previous production pointer. |
| `generation.canary.advance` | `project_admin` | Advance canary rollout to the next traffic weight step. |
| `generation.checkpoint` | `mcp_read` | Materialise merged production/sandbox DNA into a checkpoint generation (rollback SoT). Use before risky bulk edits; rollback via generation.rollback. |
| `generation.diff` | `mcp_read` | Structured diff between two resolved generation entity states (same table/project). |
| `generation.diff_vs_prod` | `mcp_read` | Diff stable production head vs pending sandbox head. Includes agent_context summary for diagnosis (counts, domains, sample paths). |
| `generation.fork` | `project_admin` | Fork production project into a new sandbox environment generation. |
| `generation.gates` | `mcp_read` | Run generation gate chain for an environment (schema, conflict, health, soak, metrics, optional manual). |
| `generation.hosting.release` | `project_admin` | Snapshot live hosting workspace into an immutable release labeled with sandbox generation. Does not swap canonical /s/ URL — follow with hosting.release.promote. |
| `generation.list` | `mcp_read` | List sandbox environment anchors for a project (names, status, CalVer, auto_created flags). error_code list_unavailable means the read failed. |
| `generation.notify` | `project_admin` | Manual generation notify — webhooks, audit log, optional in-app notification. |
| `generation.promote` | `project_admin` | Promote a sandbox environment to production (runs promotion checks first). |
| `generation.queue` | `mcp_read` | Promotion queue: pending sandbox generations with gate evaluation (REST parity). |
| `generation.realign_to_prod` | `project_admin` | Revert pending sandbox DNA toward stable production by applying inverse diff (leaf jsonb_set / path delete on sandbox anchor — no full-blob wipe). Optional path_prefixes for partial revert. |
| `generation.rollback` | `mcp_read` | Open a checkpoint as a new sandbox generation (draft). Promote the returned rollback_uuid via generation.promote to restore production DNA. |
| `generation.settings.get` | `mcp_read` | Read per-project auto_generation settings (REST parity: GET /api/generations/{id}/settings). |
| `generation.settings.patch` | `project_admin` | PATCH auto_generation settings on the production project slice. Enable auto_generation_mode so tracked MCP mutations auto-fork to sandbox. REST parity: PATCH /api/generations/{id}/settings. |
| `generation.status` | `mcp_read` | Lightweight generation + canary status for UI banners and agents. |
| `generation.supply_status` | `mcp_read` | Tenant generation supply snapshot: owner tier, open-env quota usage, auto_generation settings, pending/canary state. Alias for agents (same SoT as GET /api/sandbox/limits + generation.status). |
| `generation.timeline` | `mcp_read` | Promotion history for a project: completed, active canary (rolling_out), failed, and aborted requests (newest first). Items include strategy, traffic_weight, calver_label when recorded. |

## goals (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `goals.delete` | `project_admin` | Delete a goal by id from data.goals[]. |
| `goals.list` | `mcp_read` | List goals stored in data.goals[]. |
| `goals.progress` | `mcp_read` | Compute goal progress from finance/CRM/time BFFs. |
| `goals.upsert` | `project_admin` | Upsert a monthly goal (finance\|projects\|hours). |

## guidance (14)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `guidance.complete_step` | `mcp_read` | Mark a guidance step complete with optional artifact after server verify gates. Agent parity with SPA completePathStep. |
| `guidance.funnel_stats` | `mcp_read` | O(1) in-process Compass funnel counters for messaging-channel-bot: path_started, per-task completions, channel mix, dual_channel_attach. No database scan. |
| `guidance.get_session` | `mcp_read` | Get one guidance session by id for the authenticated user. |
| `guidance.list` | `mcp_read` | List platform Compass playbook definitions plus tenant path overlays for a project. Mirror of REST GET /api/projects/{id}/guidance/definitions. |
| `guidance.list_active_paths` | `mcp_read` | Alias for guidance.list_active_sessions — active Compass paths with percent and state. Use for agent resume decisions. |
| `guidance.list_active_sessions` | `mcp_read` | List active guidance sessions for the authenticated user in a project. |
| `guidance.list_capability_tasks` | `mcp_read` | List Platform Task Capability (PTC) atoms from platform fixture catalog Use before Compass playbooks with kind=capability or agentstack.execute task hints. |
| `guidance.match_playbook` | `mcp_read` | Match natural-language goal text to a Platform Compass playbook id using bundled intent patterns + RU/EN synonyms. Returns verifyKinds for bot/commerce paths. |
| `guidance.path_status` | `mcp_read` | Snapshot Compass verify gates for a project: botExists, botLive, botChannelAttached, hostedVitrinePublished. Use after playbook steps or before go-live checks. |
| `guidance.preview_instructions` | `mcp_read` | Read-only: route goal → playbook, quality, instruction packet, allowed actions (no LLM). |
| `guidance.recommend_skills` | `project_admin` | Outcome-oriented skill pack for a goal (no LLM, no extra tools). |
| `guidance.record_step` | `mcp_read` | Patch an active guidance session state (answers, currentNodeId, completedNodeIds, percent). Agent parity with SPA pathServerSync step PATCH. |
| `guidance.start_path` | `mcp_read` | Alias for guidance.start_session — start a Platform Compass guided path on the server. Prefer this name in agent playbooks; identical parameters and response. |
| `guidance.start_session` | `mcp_read` | Start a Platform Compass guidance session on the server (8DNA data.guidance). Use with messaging-channel-bot for cross-device resume. |

## hosting (24)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `hosting.bucket.file.get` | `mcp_read` | Read a single bucket file (text or base64, max 2MB). |
| `hosting.bucket.file.patch` | `project_admin` | Patch one UTF-8 bucket text file. search_replace does not need a prior GET. Omit bucket_id on the primary site. Identical bytes skip the disk write and gzip. A real change refreshes the .gz sibling an |
| `hosting.bucket.file.rename` | `project_admin` | Rename or move a file within a bucket. |
| `hosting.bucket.files.bulk_delete` | `project_admin` | Delete multiple files by path list or prefix. |
| `hosting.bucket.files.list` | `mcp_read` | List files in a hosting bucket (optional prefix, cursor pagination, optional q path substring filter). |
| `hosting.bucket.grep` | `mcp_read` | Search UTF-8 bucket text files for a substring or regex. Optional glob (e.g. *.html) and prefix narrow the scan. |
| `hosting.demo.status` | `mcp_read` | Public demo hosting pool status (enabled, depth, sandbox project id). |
| `hosting.demo_store.status` | `mcp_read` | Golden promo demo-store vitrine: catalog row count on sandbox, published build id, manifest bucket (`frontend.commerce.hosted_storefront.gen1`). |
| `hosting.deploy_files` | `project_admin` | Batch-upload files to a bucket and optionally publish (max 50 files per call). |
| `hosting.files.put` | `project_admin` | Upload or replace one file in a hosting bucket. Many files: one hosting.deploy_files call. List: hosting.bucket.files.list. |
| `hosting.gc.ix` | `mcp_read` | Prune orphan hosting_buckets/_ix symlinks (admin diagnostics broken_ix). |
| `hosting.project.set_primary_site` | `project_admin` | Set the project's primary public site URL when multiple sites exist (PH-18). |
| `hosting.project.status` | `mcp_read` | Project hosting plane: Host/Sell/Scale ladder, sites summary, next_actions. |
| `hosting.release.clone_site` | `project_admin` | Clone a new site from a release snapshot. |
| `hosting.release.delete` | `project_admin` | Delete an unpinned hosting release (optional bucket purge). |
| `hosting.release.list` | `mcp_read` | List site release history (newest first). |
| `hosting.release.promote` | `project_admin` | Promote a release snapshot to the live site bucket and publish. |
| `hosting.release.snapshot` | `project_admin` | Create immutable snapshot (archive bucket copy) for a site. |
| `hosting.site.delete` | `project_admin` | Remove a hosted site bucket (same as REST DELETE /api/hosting/buckets/{bucket_id}). |
| `hosting.site.edge_health` | `mcp_read` | Check nginx edge readiness (symlinks, index.html) for a hosting bucket. |
| `hosting.site.quick_start` | `project_admin` | Create bucket, upload HTML index, optional publish; returns public /s/{project_id}/{bucket_name}/ URL. |
| `hosting.site.resolve` | `mcp_read` | Resolve hosting bucket_name to bucket_id and public URL hints. |
| `hosting.storage.import_folder` | `project_admin` | Import a project storage folder into a hosting bucket. |
| `hosting.visual.inspect` | `mcp_read` | Capture viewport screenshots for visual QA (375 and 1440). Requires preview_url. |

## integrations (53)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `integrations.activate_webhook` | `mcp_read` | Register provider inbound webhook for a connection (post-OAuth or manual retry). |
| `integrations.connection_health` | `mcp_read` | Read connection health snapshot (public spec only). |
| `integrations.connector_schema` | `mcp_read` | Provider setup schema (required secrets, setup_steps, quick_setup) — call before install_recipe or create_connection so agents know which secrets= keys to pass. |
| `integrations.create_connection` | `project_admin` | Create an integration connection (REST POST /api/integrations/connections parity). Prefer integrations.install_recipe for packaged inbound webhooks. |
| `integrations.create_scenario` | `mcp_read` | Create a draft integration scenario with backing logic rule. |
| `integrations.delete_connection` | `mcp_read` | Delete an integration connection and unregister webhook bindings. |
| `integrations.dynamic_fields` | `mcp_read` | List dynamic field sources for an integration app (schema-only in Phase 1). |
| `integrations.export_connection` | `mcp_read` | Export connection public spec + config (secrets redacted). |
| `integrations.export_connection_manifest` | `mcp_read` | Export project integration connections as JSON manifest for agent tool loops. |
| `integrations.export_scenario` | `mcp_read` | Export portable scenario bundle (rule document + field maps, secrets redacted). |
| `integrations.generate_scenario` | `mcp_read` | NL → RuleDocumentV1 draft for integration scenario (copilot stub). |
| `integrations.generate_signing_secret` | `project_admin` | Rotate signing_secret server-side without reading the old value. |
| `integrations.get_app` | `mcp_read` | Get a single integration app definition by provider id. |
| `integrations.get_connection` | `mcp_read` | Get integration connection metadata (secrets redacted). |
| `integrations.get_diagnostics` | `mcp_read` | Integration Hub diagnostics rollup and worker flags. |
| `integrations.get_hubspot_relay_stats` | `mcp_read` | HubSpot platform relay rollup (7d success/denied/no-connection/missing portal). |
| `integrations.hydrate_triggers` | `mcp_read` | Hydrate integration trigger registry from connection DNA (cold start recovery). |
| `integrations.import_connection` | `project_admin` | Import connection from export JSON (optionally with secrets). |
| `integrations.import_make_preview` | `mcp_read` | Dry-run preview: map Make scenario JSON to recipe + logic block draft. |
| `integrations.import_n8n_preview` | `mcp_read` | Preview n8n workflow JSON as AgentStack steps. Does not install or run it. |
| `integrations.import_zapier_preview` | `mcp_read` | Dry-run preview: map Zapier export JSON to recipe + logic block draft. |
| `integrations.install_recipe` | `project_admin` | Install a recipe: creates connection + declarative logic rule. |
| `integrations.list_apps` | `mcp_read` | List integration app catalog entries (connectors + recipe metadata). |
| `integrations.list_connections` | `mcp_read` | List integration connections for project or personal scope. Each row is id plus spec.provider, spec.label, and spec.status. webhook_activation, routing, and logic_id are null here. The nested card_row |
| `integrations.list_dlq_deliveries` | `mcp_read` | List integration outbound deliveries in DLQ or terminal failure state. |
| `integrations.list_inbox_events` | `mcp_read` | List durable integration inbox audit rows for a project. |
| `integrations.list_issues` | `mcp_read` | Cluster failed outbound deliveries into issues (Hookdeck-style). |
| `integrations.list_module_registrations` | `mcp_read` | List module registrations persisted on connection DNA rows. |
| `integrations.list_modules` | `mcp_read` | List reusable sub-scenario module stubs. |
| `integrations.list_recipes` | `mcp_read` | List integration recipe templates (Stripe, GitHub, Telegram, etc.). |
| `integrations.list_scenario_runs` | `mcp_read` | List recorded test/run history for a scenario. |
| `integrations.list_scenarios` | `mcp_read` | List integration scenarios for a project scope. |
| `integrations.mailbox` | `mcp_read` | Read or send mail on a connected Gmail or Microsoft mailbox. op is list, get, or send. Tokens stay on the connection. list returns id, subject, from, snippet (max 10). get returns plain text. send del |
| `integrations.migrate_legacy_webhooks` | `mcp_read` | Copy projects.config.webhooks into integration_connection rows. |
| `integrations.oauth_begin` | `mcp_read` | Start OAuth 2.0 PKCE flow for an integration connection (returns authorization_url). |
| `integrations.poll_connection` | `mcp_read` | Run connector polling for a connection (cursor watermark in config.poll_state). |
| `integrations.preview_scenario_maps` | `mcp_read` | Evaluate scenario field maps against a sample payload (secrets redacted). |
| `integrations.process_inbox_batch` | `mcp_read` | Process durable inbound integration inbox (worker-style batch). |
| `integrations.process_outbound_batch` | `mcp_read` | Process outbound webhook delivery work queue (webhooks.outbound batch). |
| `integrations.process_polling_batch` | `mcp_read` | Poll due integration connections (config.poll_enabled, cursor watermark). |
| `integrations.publish_scenario` | `mcp_read` | Publish scenario (live) and enable backing logic. |
| `integrations.rebind_triggers` | `mcp_read` | Re-sync enabled logic rule triggers for a project or all connection projects. |
| `integrations.refresh_token` | `mcp_read` | Refresh OAuth access token for a connection when expired (protected storage). |
| `integrations.register_module` | `mcp_read` | Register a reusable sub-scenario module stub on a parent connection. |
| `integrations.replay_delivery` | `mcp_read` | Replay outbound delivery by durable delivery_id (execution log). |
| `integrations.replay_delivery_url` | `mcp_read` | Replay outbound POST to an explicit target_url (same as REST deliveries/replay query). |
| `integrations.replay_inbox` | `mcp_read` | Replay a durable inbox event through the test-hook intake path. |
| `integrations.rotate_secret` | `project_admin` | Rotate a protected secret key for a connection. |
| `integrations.save_scenario_draft` | `mcp_read` | Save scenario draft (spec and/or logic patch). Parity with REST POST /scenarios/{id}/draft. |
| `integrations.test_hook` | `mcp_read` | Dry-run inbound hook verify + normalize (sync dispatch to logic). |
| `integrations.test_scenario_step` | `mcp_read` | Dry-run a scenario step via logic simulation. |
| `integrations.unpublish_scenario` | `mcp_read` | Unpublish scenario (pause) and disable backing logic. Parity with REST unpublish. |
| `integrations.update_connection` | `mcp_read` | Patch integration connection metadata (label, status, config, direction). |

## knowledge (41)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `knowledge.acceptance.case.run` | `bots_run` | MCP tool knowledge.acceptance.case.run |
| `knowledge.acceptance.case.upsert` | `bots_run` | MCP tool knowledge.acceptance.case.upsert |
| `knowledge.acceptance.list` | `bots_run` | MCP tool knowledge.acceptance.list |
| `knowledge.acceptance.run` | `bots_run` | MCP tool knowledge.acceptance.run |
| `knowledge.access.grant` | `bots_run` | MCP tool knowledge.access.grant |
| `knowledge.access.list` | `bots_run` | MCP tool knowledge.access.list |
| `knowledge.access.revoke` | `bots_run` | MCP tool knowledge.access.revoke |
| `knowledge.config.get` | `bots_run` | MCP tool knowledge.config.get |
| `knowledge.config.patch` | `bots_run` | Patch knowledge DNA config + safety playbooks (same as PATCH /knowledge/config). gene_pack merges by name; safety_playbooks default write_mode is patch (per-playbook overlay — other playbook ids are k |
| `knowledge.content.delete` | `bots_run` | Deactivate or hard-delete a KB document by source_doc_id. Same as DELETE /knowledge/content/{id}. |
| `knowledge.content.get` | `bots_run` | Get one KB document by source_doc_id (chunks + joined text). Always call this before knowledge.content.patch replace. text uses the same \n\n join as list; check text_chars before rewriting. |
| `knowledge.content.list` | `bots_run` | List operator KB documents. Each item includes title, source_doc_id, chunk_count, and joined text (same as GET /knowledge/content). |
| `knowledge.content.patch` | `bots_run` | Patch a KB document: metadata (title, gene_stems, …) and/or body. Body write_mode is replace (full text), append, or prepend — never a silent section splice. Same as PATCH /knowledge/content/{id}. |
| `knowledge.correction.propose` | `bots_run` | MCP tool knowledge.correction.propose |
| `knowledge.doctor.run` | `bots_run` | MCP tool knowledge.doctor.run |
| `knowledge.eval.run` | `bots_run` | MCP tool knowledge.eval.run |
| `knowledge.eval.run_all` | `bots_run` | MCP tool knowledge.eval.run_all |
| `knowledge.export` | `bots_run` | MCP tool knowledge.export |
| `knowledge.gc.reconcile` | `bots_run` | MCP tool knowledge.gc.reconcile |
| `knowledge.gc.status` | `bots_run` | MCP tool knowledge.gc.status |
| `knowledge.gene_pack.pinned_shape.upsert` | `bots_run` | MCP tool knowledge.gene_pack.pinned_shape.upsert |
| `knowledge.health` | `bots_run` | MCP tool knowledge.health |
| `knowledge.import` | `bots_run` | MCP tool knowledge.import |
| `knowledge.journal.get` | `bots_run` | MCP tool knowledge.journal.get |
| `knowledge.journal.list` | `bots_run` | MCP tool knowledge.journal.list |
| `knowledge.kb.heal_index` | `bots_run` | Realign DNA storage_ref.sha256 to on-disk sqlite for a KB collection (no wipe). Fixes rag_index_sha256_mismatch. Default collection_id=content_registry. |
| `knowledge.kb.ingest` | `bots_run` | MCP tool knowledge.kb.ingest |
| `knowledge.phenotype.upsert` | `bots_run` | MCP tool knowledge.phenotype.upsert |
| `knowledge.playground` | `bots_run` | Full bot simulate with RAG/safety/gene-lock trace (REST playground). Heavy LLM: one per sync agentstack.execute. Prefer over raw rag.search for mentor fidelity. Same budget as bots.simulate. |
| `knowledge.policy_templates.apply` | `bots_run` | Apply a platform policy template onto the tenant: plan_instructions append (marker [policy:id]), merge crisis match_any (keep tenant template), union gene_pack.situational_needles, optional safety_fai |
| `knowledge.policy_templates.list` | `bots_run` | Platform policy templates (synthesis / crisis / situational needles / gate). Same catalog as GET /knowledge/policy-templates. Apply writes 8DNA via existing prompt+config PATCH — not a second bus. |
| `knowledge.policy_templates.status` | `bots_run` | Read-only policy snapshot: synthesis [policy:id] markers in plan_instructions, playbook ids, gate mode, situational needle count. Same as GET /knowledge/policy-templates/status. |
| `knowledge.principal.simulate` | `bots_run` | MCP tool knowledge.principal.simulate |
| `knowledge.project_ai_keys.patch` | `bots_run` | Patch tenant project AI keys in project.protected (KeySelector). Same storage as PUT /api/projects/{id}/ai-keys — no secret values in GET. |
| `knowledge.project_ai_keys.status` | `bots_run` | AI key presence + ai_settings flags (no secret values). |
| `knowledge.project_secrets.apply` | `bots_run` | Apply Unity tenant secrets: OpenAI → project.protected, GetCourse/MAX → Integration Hub protected. Pass openai/getcourse/max fields. |
| `knowledge.prompt.get` | `bots_run` | Grounded assistant preamble + plan_instructions (same as GET /knowledge/prompt). Returns full strings plus system_preamble_chars / plan_instructions_chars. Always GET before knowledge.prompt.patch rep |
| `knowledge.prompt.patch` | `bots_run` | Patch grounded assistant system_preamble and/or plan_instructions (same as PATCH /knowledge/prompt). omit-unset keeps unsent keys; a sent field is the full string. Writes prompt_settings leaf via patc |
| `knowledge.registry.list` | `bots_run` | MCP tool knowledge.registry.list |
| `knowledge.registry.upsert` | `bots_run` | MCP tool knowledge.registry.upsert |
| `knowledge.retrieve.probe` | `bots_run` | MCP tool knowledge.retrieve.probe |

## logic (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `logic.attach_template` | `logic_write` | Install a template from the built-in catalog into the current project. |
| `logic.create` | `logic_write` | Create new Logic Engine rule for project. |
| `logic.delete` | `logic_write` | Delete a Logic Engine rule. |
| `logic.diff_versions` | `mcp_read` | Diff two rule snapshots (F2.9). |
| `logic.dry_run` | `logic_write` | Simulate a logic rule execution without side effects (F2.5). |
| `logic.execute` | `logic_write` | Execute a Logic Engine rule immediately. |
| `logic.export_json` | `mcp_read` | Export a logic rule as JSON for backup / cross-project copy. |
| `logic.flush_batch` | `logic_write` | Force flush logic batch for a project (saves all accumulated rules immediately) |
| `logic.get` | `mcp_read` | Get detailed information about a Logic Engine rule. |
| `logic.get_commands` | `mcp_read` | Get list of available commands for triggers. |
| `logic.get_processors` | `mcp_read` | Get list of available processors (same as processors.list but in logic context). |
| `logic.import_json` | `logic_write` | Import a logic rule from JSON (creates a new rule). |
| `logic.install_blueprint` | `logic_write` | Install a catalog blueprint into the project (alias of attach_template with field_values). |
| `logic.list` | `mcp_read` | List all Logic Engine rules for project. |
| `logic.list_versions` | `mcp_read` | List historical snapshots stored on a rule (F2.9). |
| `logic.mcp_actions_catalog` | `mcp_read` | List MCP actions addressable from logic rules. |
| `logic.restore_version` | `logic_write` | Roll a rule back to a prior snapshot (F2.9). |
| `logic.signals_catalog` | `mcp_read` | List signal channels the logic engine can subscribe to. |
| `logic.update` | `logic_write` | Update existing Logic Engine rule. |

## mentor (8)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `mentor.eval.retrieval` | `bots_run` | MCP tool mentor.eval.retrieval |
| `mentor.eval.run` | `bots_run` | MCP tool mentor.eval.run |
| `mentor.eval.run_all` | `bots_run` | MCP tool mentor.eval.run_all |
| `mentor.export` | `bots_run` | MCP tool mentor.export |
| `mentor.journal.list` | `bots_run` | MCP tool mentor.journal.list |
| `mentor.kb.ingest` | `bots_run` | MCP tool mentor.kb.ingest |
| `mentor.principal_link.bind_code` | `bots_run` | MCP tool mentor.principal_link.bind_code |
| `mentor.principal_link.upsert` | `bots_run` | MCP tool mentor.principal_link.upsert |

## messaging (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `messaging.get_auth_email_readiness` | `social_read` | Probe auth email plane — provider secrets, email_confirmation + password_reset + email_otp templates. |
| `messaging.inbox.get` | `social_read` | Read one thread as text for the signed-in user. |
| `messaging.inbox.list` | `social_read` | List inbox cards for the signed-in user. Bodies are not included. |
| `messaging.inbox.reply` | `social_read` | Reply to the latest noticed letter from the signed-in mailbox. |
| `messaging.list_suppressions` | `social_read` | List ecosystem email suppression list (bounces/complaints). |
| `messaging.mail.send` | `social_read` | Send one letter from the signed-in @agentstack.tech address. |
| `messaging.mailbox.agent.set` | `social_read` | Save or clear the agent id on the signed-in mailbox. A later letter queues that agent. This call does not start a run. |
| `messaging.mailbox.alias.add` | `social_read` | Add one alias on the signed-in mailbox. Cap is 5. |
| `messaging.mailbox.alias.remove` | `social_read` | Remove one alias from the signed-in mailbox. |
| `messaging.mailbox.claim` | `social_read` | Claim local@agentstack.tech for the signed-in user. |
| `messaging.mailbox.get` | `social_read` | Read the signed-in user's @agentstack.tech address, aliases, and notify toggles. Only the mailbox leaf is read. The rest of the profile stays in Postgres. |
| `messaging.send_email` | `social_read` | Send email to any address (ecosystem admin). Use template_name + template_data for auth templates. |

## notifications (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `notifications.cancel_push` | `mcp_read` | Cancel pending push items by correlation key prefix. |
| `notifications.delete_source` | `mcp_read` | Remove a notification source by id (parity with DELETE notification-sources). |
| `notifications.deliver` | `mcp_read` | Send one notice on email, in-app, web push, webhook, or a bound bot channel (telegram, max, whatsapp, instagram, slack, discord, sms). |
| `notifications.inbox_list` | `mcp_read` | List Fabric in-app inbox rows for a project (parity with GET /api/notifications/{project_id}). |
| `notifications.list_prefs` | `mcp_read` | Messenger prefs plus category×channel matrix and enrolled bot sources. |
| `notifications.mark_read` | `mcp_read` | Mark one project inbox notification as read (parity with PATCH …/read). |
| `notifications.register_category` | `mcp_read` | Register a logical push category in user messenger prefs (persisted). |
| `notifications.send_push` | `mcp_read` | Enqueue OS web push for a user (self or admin). |
| `notifications.send_with_fallback` | `mcp_read` | Deliver with category fallback chain (security/auth/billing) and in_app escalation when all channels fail. Prefs are read on the server; the profile is not returned. |
| `notifications.subscribe_push` | `mcp_read` | Returns VAPID status; browser must still call PushManager.subscribe. |
| `notifications.update_prefs` | `project_admin` | Patch messenger prefs and/or category×channel notification matrix. One channel: patch {category, channel, enabled} calls set_category_channel and does not replace the rest of the matrix. work_graph + |

## organelle (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `organelle.execute` | `mcp_read` | Invoke a registered organelle op via dispatch (ring_pool / cell_store today; storage & work_queue via related tools). |

## payments (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `payments.create` | `payments` | Create a new payment transaction. |
| `payments.get` | `payments` | Get detailed payment information by payment ID. |
| `payments.get_balance` | `payments` | Get current wallet balance for a project. |
| `payments.list_transactions` | `payments` | List all payment transactions for a project. |
| `payments.refund` | `payments` | Refund a completed payment. |

## permissions (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `permissions.effective` | `mcp_read` | Effective permission surface: token caps ∩ RBAC role ∩ buff limits. Optional resource + path_prefixes for Field Access Policy write preflight. |

## preflight (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `preflight.check` | `—` | Tenant mutation preflight — supply, limits, optional RBAC permission. |
| `preflight.simulate` | `mcp_read` | IAM-style dry-run: would this session be allowed to call MCP action X? Returns L1 caps, L2 RBAC, and optional FAP path probe. |

## processors (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `processors.execute` | `logic_write` | Execute a processor directly. |
| `processors.get_metadata` | `mcp_read` | Get detailed metadata for a specific processor. |
| `processors.list` | `mcp_read` | List all available processors with their metadata. |

## projects (33)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `projects.add_user` | `project_admin` | Add a user to a project (requires manage_users or owner on that project). |
| `projects.archive` | `project_admin` | Archive a project (ledger-safe archive twin of POST /archive). |
| `projects.cancel_deletion` | `mcp_read` | Cancel a scheduled project deletion (PSDP cancel twin). |
| `projects.catalog_summaries` | `mcp_read` | Batch light KPI rows for project catalog tiles (ids max 40). |
| `projects.create_project` | `project_admin` | Create a new project. |
| `projects.create_project_anonymous` | `project_admin` | Anonymous bootstrap: one-time anon_ask_* key + project_id. Upgrade: auth.email_otp.send purpose=convert with same X-API-Key — no convert tool. |
| `projects.delete_project` | `project_admin` | Delete a project (PSDP). |
| `projects.deletion_status` | `mcp_read` | Read PSDP deletion status for a project. |
| `projects.execute_deletion` | `project_admin` | Execute scheduled or immediate deletion with execution token. |
| `projects.get_data` | `mcp_read` | Read one nested path under project.data (REST GET /projects/{id}/data?path=). The leaf value stays whole. Pass detail=compact to sketch a wide value. |
| `projects.get_deletion_inventory` | `mcp_read` | Pre-delete inventory: blockers, warnings, and execution_token for safe delete. |
| `projects.get_my_data` | `mcp_read` | Read the signed-in member's project data (REST GET /projects/{id}/users/me/data). A path returns that leaf whole; siblings stay in Postgres. Omit path and Postgres returns only top-level names and typ |
| `projects.get_notify_policy` | `mcp_read` | Read project.data.notify_policy (channel plane tenant defaults). |
| `projects.get_project` | `mcp_read` | Get one project. |
| `projects.get_projects` | `mcp_read` | Get list of projects for the current user. |
| `projects.get_stats` | `mcp_read` | Get project statistics. |
| `projects.get_users` | `mcp_read` | Get list of users in a project. |
| `projects.import_by_key` | `project_admin` | Import accessible projects using a user API key (operator-sensitive). |
| `projects.limits.get` | `mcp_read` | Project effective limits — buffs + bot create gate metadata. |
| `projects.membership.repair` | `project_admin` | Repair project membership / accessible_ids lag for the caller. |
| `projects.orchestrator.export_pack` | `mcp_read` | Export portable ProjectOrchestratorPack JSON. |
| `projects.orchestrator.get` | `mcp_read` | Get project_orchestrator 8DNA leaf config. |
| `projects.orchestrator.import_from_asset` | `project_admin` | Fulfill orchestrator pack from commerce asset or raw pack dict. |
| `projects.orchestrator.patch` | `project_admin` | Patch project_orchestrator leaf (dna_set_project_data_path). |
| `projects.patch_data` | `project_admin` | Leaf SET on project.data (REST PATCH /projects/{id}/data). write_mode=replace sets the path; merge overlays an object; delete removes it. Do not use projects.update_project for nested bot commands or |
| `projects.patch_my_data` | `project_admin` | Leaf write on the signed-in member's project data (REST PATCH /projects/{id}/users/me/data). write_mode replace sets the path; merge overlays an object. |
| `projects.presets.list` | `mcp_read` | List project create presets / templates. |
| `projects.put_notify_policy` | `project_admin` | Merge patch into project.data.notify_policy. Paid tenants with auto_generation_mode require env_uuid (sandbox anchor). |
| `projects.readiness.get` | `mcp_read` | Lightweight project readiness — supply + knowledge health skim. |
| `projects.remove_user` | `project_admin` | Remove a user from a project (requires manage_users or owner on that project). |
| `projects.schedule_deletion` | `project_admin` | Schedule project deletion after inventory + execution token. |
| `projects.update_project` | `project_admin` | Update an existing project. |
| `projects.update_user_role` | `project_admin` | Update a user's role in a project (requires manage_users or owner; cannot assign owner via this tool). |

## rag (18)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rag.collection_create` | `rag.write` | Create a new RAG knowledge base collection for the current project. |
| `rag.collection_delete` | `rag.write` | Delete a RAG collection and all its documents. This is irreversible. |
| `rag.collection_get` | `rag.read` | Get one RAG collection by id (verify-after-write read model). |
| `rag.collection_list` | `rag.read` | List all RAG collections for the current project. |
| `rag.document_add` | `rag.write` | Add a text document to a RAG collection. |
| `rag.document_add_batch` | `rag.read` | Add multiple documents to a RAG collection in one call. |
| `rag.document_delete` | `rag.write` | Remove a document (all its chunks) from a collection by source_doc_id. |
| `rag.document_list` | `rag.read` | List document chunks stored in a collection. |
| `rag.export` | `rag.read` | Export accessible collection chunks (access-tier filtered) as JSON documents. |
| `rag.health` | `rag.read` | RAG persistence mode and collection stats for the current project scope. |
| `rag.ingest_from_storage` | `rag.read` | Ingest text extracted from a Storage file into a RAG collection. |
| `rag.ingest_job_cancel` | `rag.read` | Cancel a queued or in-flight rag_ingest job (idempotent). |
| `rag.ingest_job_status` | `rag.read` | Poll async batch ingest job status (work_queue rag_ingest). |
| `rag.memory_add` | `rag.write` | Store a conversation turn in AI memory. |
| `rag.memory_get` | `rag.read` | Get the most recent conversation turns from memory (chronological order). |
| `rag.memory_search` | `rag.read` | Semantically search past conversation turns. |
| `rag.search` | `rag.read` | Semantic search over a RAG collection. |
| `rag.warm` | `rag.read` | Pre-load collection vectors into process L1 before search traffic. |

## rbac (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rbac.assign_role` | `mcp_read` | Assign a role to a user in a project. Requires manage_users or owner. Cannot assign owner via this tool. |
| `rbac.check_permission` | `mcp_read` | Check if a user has a permission in a project. If user_id is omitted, checks current user. |
| `rbac.get_roles` | `mcp_read` | Get list of roles for a project (system + custom). Returns id, name, permissions_bitmap, permissions, is_system. |
| `rbac.revoke_role` | `project_admin` | Revoke a role from a user in a project. User will get default viewer role. Requires manage_users or owner. Cannot revoke owner. |

## safety (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `safety.evaluate` | `bots_run` | MCP tool safety.evaluate |

## scheduler (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `scheduler.cancel_task` | `scheduler` | Cancel/remove a scheduler task (removes from pool and disables in database). |
| `scheduler.clear_pool_tasks` | `scheduler` | Clear all tasks from the execution pool (does not delete from database). |
| `scheduler.create_task` | `scheduler` | Create a new scheduled task for automated execution. |
| `scheduler.delete_pool_task` | `scheduler` | Remove a specific task from the execution pool (does not delete from database). |
| `scheduler.delete_task` | `project_admin` | Soft-disable alias for scheduler.cancel_task (sets enabled=false; row remains). Prefer scheduler.cancel_task for new agents. |
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
| `seo.cache.clear` | `mcp_read` | Invalidate neural SEO meta + robots/sitemap caches; optionally ping IndexNow. |
| `seo.health.check` | `mcp_read` | SEO registry version, indexable path count, and hosting funnel readiness. |
| `seo.meta.get` | `mcp_read` | Fetch dynamic meta tags for a public marketing path (title, description, OG, JSON-LD). |

## services (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `services.apply_delivery_slot` | `mcp_read` | Grant phase-1 studio delivery slot on tenant head project (data.ecosystem.buffs[] protected). After signed main SoW. |
| `services.diagnostic_sow_draft` | `mcp_read` | Render a studio diagnostic (kickoff) SoW markdown from the services catalog fixture. Staff-only on ecosystem project 1. |
| `services.kickoff_slot_status` | `mcp_read` | Read kickoff slot status for CRM contact (optional payer user buff merge). |

## showcase (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `showcase.catalog.diff_vs_fixture` | `mcp_read` | Drift report: DNA catalog entry ids vs git fixture. |
| `showcase.catalog.export_fixture` | `mcp_read` | Export merged catalog as fixture-shaped JSON for git sync. |
| `showcase.catalog.get` | `mcp_read` | Merged public showcase catalog (ecosystem DNA + fixture seed). |
| `showcase.catalog.list_entries` | `mcp_read` | Lightweight list of showcase catalog cards (merged). required_scope: ecosystem project_id=1. Other project ids return invalid_parameters. |
| `showcase.catalog.patch_entry` | `project_admin` | Partial update of one catalog card on ecosystem DNA. |
| `showcase.catalog.reorder` | `mcp_read` | Reorder showcase cards (entry_order on ecosystem DNA). |
| `showcase.catalog.upsert_entry` | `project_admin` | Upsert one showcase catalog card on ecosystem DNA (pid=1). |
| `showcase.health.probe` | `mcp_read` | Probe canonical /s/ URLs and stamp ecosystem health. |
| `showcase.settings.patch` | `project_admin` | Patch showcase gallery settings on ecosystem DNA. |
| `showcase.tenant.register` | `mcp_read` | Register an independent tenant on the public showcase (gallery card only). |

## social (68)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `social.channel_invites.accept` | `social_read` | Accept a channel invite from inbox. |
| `social.channel_invites.decline` | `social_read` | Decline a channel invite. |
| `social.channel_invites.direct` | `social_read` | Create direct channel invite to a user (owner). |
| `social.channel_invites.inbox` | `social_read` | List pending channel invites for the current user. |
| `social.channel_invites.link_token` | `social_read` | Create shareable link token for a channel (owner). |
| `social.channel_invites.redeem` | `social_read` | Redeem channel invite token. |
| `social.channels.delete` | `project_admin` | Delete a channel (owner) and unpublish from public index. |
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
| `social.support.ai_binding_health` | `mcp_read` | Read support AI binding health (agent, mode, warnings). |
| `social.support.assign` | `project_support` | Staff: assign a support ticket to a staff user id (or unassign with null). |
| `social.support.config.patch` | `project_admin` | Merge project support config (ai_agent_uuid, ai_mode, entry gates). Owner or admin. |
| `social.support.eligibility` | `project_support` | Batch read: for home project ids the user can access, returns whether end-user private support and/or public Messenger support lounge are enabled (channel id public support lounge channel for the project when public). |
| `social.support.history` | `project_support` | Read project support thread for the authenticated user (channel project support channel for the authenticated user). |
| `social.support.inbox` | `project_support` | Staff inbox preview list for a project (requires support access). |
| `social.support.request_human` | `project_support` | End user: request a human handoff for the current ticket (pending when allowed). |
| `social.support.search_projects` | `social_read` | Search projects the user may contact via support (titles ranked vs query). Returns support entry flags per row. |
| `social.support.send` | `project_support` | Send a message in the user's support thread for a project (user) or staff reply when permitted. |
| `social.support.transition` | `project_support` | Staff: transition a support ticket lifecycle status (requires support desk access). |
| `social.users.lookup` | `social_read` | Find users by id, email, or @username (no email in response). |
| `social.users.public_cards` | `social_read` | Public display cards for user ids (no friendship check). |

## storage (13)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `storage.admin_list_rows` | `storage.read` | Ecosystem admin: list 8DNA rows with data.ecosystem.storage (project_id, dna_user_id, file counts, bytes). |
| `storage.admin_reconcile` | `storage.read` | Ecosystem admin (platform hub): fix disk↔8DNA drift for any row. Tenants: use storage.reconcile with scope. |
| `storage.admin_scan` | `storage.read` | Ecosystem admin (platform hub): compare on-disk files with 8DNA storage registry for (project_id, dna_user_id). Tenants: use storage.scan. |
| `storage.bind_entity` | `storage.read` | Attach entity_ref to a storage file card. |
| `storage.delete_file` | `storage.write` | Deletes a file from ecosystem storage (JSON registry + blob). Requires write access for user scope; owner/admin for project scope. |
| `storage.get_hub_summary` | `storage.read` | Hub v4 aggregate: permanent/temp totals, hosting bytes, pool cap, sites count. |
| `storage.get_project_usage_summary` | `storage.read` | Owner/admin: total permanent/temp bytes across all members and project slice, plus project_pool_limit_bytes from the project billing tier. |
| `storage.get_quota` | `storage.read` | Returns storage quota for the current user or project slice: used/limit bytes, tier_basis (project_owner vs fallback), owner_user_id. Pass project_id or rely on API key / session project. scope=user ( |
| `storage.list_by_entity` | `storage.read` | List files bound to an entity_ref. |
| `storage.list_files` | `storage.read` | Lists files in an ecosystem storage folder for the current user or project slice. Pass folder key (use '_' for root) or omit to list all folders (heavy). Set include_folders=1 to return folder_index f |
| `storage.reconcile` | `storage.read` | Project RBAC: fix disk↔8DNA drift for the resolved row (dry_run default true). scope=user requires write; scope=project requires owner/admin. Prefer over storage.admin_reconcile for tenants. |
| `storage.scan` | `storage.read` | Project RBAC: compare on-disk files with 8DNA storage registry for the resolved row. scope=user (default) scans caller member row; scope=project scans project slice (user_id=0). Prefer over storage.ad |
| `storage.upload` | `storage.read` | Binary upload uses REST POST /api/storage/upload (multipart/form-data). This MCP action returns the contract + quota gate — call storage.get_quota first. |

## system (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `system.ping` | `—` | Health check tool — always succeeds, no authentication required. |

## time (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `time.list` | `—` | List recent time entries. |
| `time.log` | `—` | Log a time entry (minutes) optionally linked to crm_deal. |
| `time.report` | `—` | Aggregate time report for project. |

## user (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `user.apikeys.create` | `project_admin` | Create a personal AgentStack PAT on the signed-in user (ecosystem identity). |
| `user.apikeys.list` | `mcp_read` | List personal PATs (metadata only). Optional project_id filters agent keys scoped to that project. |
| `user.apikeys.revoke` | `project_admin` | Revoke (soft-delete) a personal PAT by key_id. |
| `user.apikeys.rotate` | `project_admin` | Rotate a personal PAT; returns a new secret once. |

## vertical_demo (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `vertical_demo.repair_due_dates` | `mcp_read` | Set stage-based due_at on CRM deals missing a deadline (idempotent). |
| `vertical_demo.seed_apply` | `mcp_read` | Apply demo CRM deals, contacts, expenses, goals. |
| `vertical_demo.seed_clear` | `mcp_read` | Remove demo-tagged entities. |
| `vertical_demo.seed_plan` | `mcp_read` | Dry-run demo seed for vertical workspace. |

## vertical_workspace (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `vertical_workspace.bootstrap` | `project_admin` | Bootstrap vertical workspace after register: CRM pipeline preset, persona leaf, freelancer logic templates (idempotent). |

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
| `web_push.get_health` | `mcp_read` | Per-user Web Push health (same payload as GET /api/push/health): VAPID, canDeliver, subscription/outbox counts, process-local worker stats. |

<!-- END:AUTOGEN-CAPABILITY-MATRIX -->

## References

- `docs/plugins/CONTEXT_FOR_AI.md` — intent routing by domain.
- `docs/adr/PUBLIC_DOCS_CLASSIFICATION.md` — audience policy.
