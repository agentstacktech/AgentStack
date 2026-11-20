# AgentStack MCP Capability Matrix

> **Integrator reference.** Action IDs match `GET https://agentstack.tech/mcp/actions`. Tenant-facing catalog only; platform-operator actions are omitted.

- Source: in-process `mcp.routes._build_mcp_actions_catalog_payload`
- Generated: 2026-11-20 08:52 UTC
- Audience: **public (tenant only)**
- Total actions: **953**
- Gene: `repo.plugins.capability_routing.gen1` · `docs.public.classification.gen1`

<!-- BEGIN:AUTOGEN-CAPABILITY-MATRIX -->

## agents (68)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agents.approve_run` | `agents_run|agents_approve` | Approve a run stuck in waiting_for_approval and resume execution. |
| `agents.automation_map` | `mcp_read` | Fleet automation aggregate: triggers, Logic rule ids, support ownership. |
| `agents.create` | `agents_admin` | Create a new agent row (draft AgentSpec). |
| `agents.create_from_template` | `agents_admin` | Create an agent row from a built-in template (atomic merge on server). Fleet catalog rows are production-scoped; env_uuid applies when node_id binds the new agent onto a sandbox plan slice. |
| `agents.delete` | `agents_admin` | Delete an agent row. |
| `agents.ensure_agent` | `agents_run` | Resolve or provision a plan-node executor. Read-only by default (effect.kind=read); create=true provisions from top compatible template with bind_node=False, validates eligibility, then returns next_a |
| `agents.fleet_diagnostics` | `agents_run` | Project fleet diagnostics: runs, queue, orchestrator agent binding. |
| `agents.fork` | `agents_admin` | Fork an agent to a new generation (DNA row). |
| `agents.gates` | `agents_run` | Evaluate promotion gates for an agent. |
| `agents.get` | `agents_run` | Get a single agent by UUID (default owner=project; personal rows need owner=user). |
| `agents.graph_file_edit` | `project_admin` | Search/replace on a linked file, upload new blob, swap ref, delete old file. |
| `agents.graph_file_get` | `storage.read` | Read a graph-linked file card and a text window (no work_next). |
| `agents.graph_file_list` | `storage.read` | List storage_refs on a node plus storage.list_by_entity(plan_node). |
| `agents.graph_file_put` | `project_admin` | Upload bytes to storage and append StorageRefV1 on a plan node. |
| `agents.graph_health` | `mcp_read` | Work Graph health snapshot (loop compliance, blocked ratio, structural cycle). Thin read wrapper over diagnostics.consistency.scan graph_health block. |
| `agents.health` | `mcp_read` | Fleet ECS index health for a project (agents/bots organelle diagnostics). |
| `agents.import_from_asset` | `agents_admin` | Create agent from commerce asset extensions.agent_fleet preset. |
| `agents.instruction_rules_list` | `mcp_read` | List merged platform instruction rules (fixture + ecosystem overlay). |
| `agents.instruction_rules_upsert` | `project_admin` | Upsert one instruction rule on ecosystem project overlay (8DNA leaf). |
| `agents.kill` | `agents_admin` | Set agent lifecycle state to killed (hard stop for new runs). |
| `agents.list` | `agents_run` | List Agents Fleet rows. Default projection=summary reads the fleet index card (uuid, title, status), not agent_spec. projection=full loads the agent row. |
| `agents.list_pending_approvals` | `mcp_read` | List runs waiting for approval across project or personal agent scope. |
| `agents.metrics` | `agents_run` | Read rollup metrics for an agent (ecosystem.agents_metrics). |
| `agents.orchestrate` | `mcp_read` | Run the project AI orchestrator for a channel (workspace\|messenger\|bot\|mcp\|api). |
| `agents.plan_apply_affinities` | `project_admin` | Pin affinity hints onto plan DNA. Pass pin_skills and/or pin_genes; at least one must be true. When scope is agent, pass agent_id. |
| `agents.plan_apply_proposal` | `project_admin` | Apply a planner proposal to the Work Graph with CAS (if_match_revision). Pass proposal from plan_propose and base_graph_revision unchanged. |
| `agents.plan_archive_branch` | `mcp_write` | Preview or apply archive for a plan subtree (root_id). dry_run=true returns archive_ids and bytes_saved_estimate; apply sends plan_patch archive ops. |
| `agents.plan_claim` | `mcp_write` | Claim pending plan leaves as in_progress for one agent (parallel work, WIP capped). Server-managed on claim: status, agent_id, meta.claimed_at\|lease_until\|heartbeat_at. Preserves title/objective/predi |
| `agents.plan_compaction_preview` | `mcp_read` | Read-only preview of aged-terminal compaction (same math as agents.graph_health compaction_preview). No DNA write. |
| `agents.plan_execute` | `project_admin` | Run orchestrator focused on current plan graph (alias for orchestrate with plan focus). Returns plan_revision / completion decision when available. |
| `agents.plan_get` | `mcp_read` | Read the Work Graph (8DNA ecosystem.orchestrator.task_list). Default compact slice (revision + flat rows); fleet rollup metrics are opt-in via include_metrics or expand=metrics. Same MCP execute batch |
| `agents.plan_heal_leaf` | `mcp_write` | Compact aged terminals and spill heavy node bodies into work_graph/chunks storage without changing plan semantics. Use when graph_limits.spill_incomplete, plan_graph_bytes_high advisories, or plan_gra |
| `agents.plan_ingest` | `mcp_read` | Append diagnostic or work-intake findings as plan/Work Graph tasks. intake_mode=diagnostic (default) or work. Existing claimed nodes are left alone; matching issues bump occurrence_count. |
| `agents.plan_patch` | `project_admin` | Merge plan graph nodes (deep by id). Pass if_match_revision from plan_get for CAS (fleet/autonomous writers must send it). F1: when auto_generation_mode is on, pass env_uuid from sandbox fork; diff pa |
| `agents.plan_pin` | `mcp_read` | Write goal pin_inventory without claiming a node or consuming WIP. Same pin/unpin/collapse/expand params as plan_claim heartbeat, minus the lease. |
| `agents.plan_preview_affinities` | `mcp_read` | Preview suggested gene/skill hints on plan nodes (read-only, no DNA pin). |
| `agents.plan_process_backlog` | `mcp_read` | Scan decomposition backlog (decomposition_status=needed, no execution decomposition) and build planner proposals. Claims planner-parent lease before decompose when required_role=planner (even when aut |
| `agents.plan_propose` | `mcp_read` | Build a validated planner proposal (draft) for create_plan or decompose_node. Does not mutate task_list — call agents.plan_apply_proposal to persist. |
| `agents.plan_reclaim_stale` | `mcp_read` | Return in_progress plan nodes with expired claim lease to ready (or pending if not eligible). Clears agent_id + lease meta; stamps meta.last_claim. Mutating DNA write — not read-only. |
| `agents.plan_reconcile_diagnostics` | `mcp_read` | Auto-complete open diagnostic plan nodes when live findings are cleared (statuses_toward_completed: typically pending→verifying→completed). |
| `agents.plan_recovery_scan` | `mcp_read` | Read-only scan for verification_failed repair candidates and stale claims. Reason codes use recovery_engine SoT (verification_failed, expired_claim). |
| `agents.plan_refresh_affinities` | `mcp_read` | Deprecated alias: use plan_preview_affinities or plan_apply_affinities. |
| `agents.plan_release` | `mcp_write` | Release your own in_progress plan claim (voluntary). Use when wip_full blocks new work and the lease is still yours. |
| `agents.plan_suggest_tree` | `mcp_read` | Suggest a nested plan tree draft from a goal (read-only). Does not mutate task_list — use plan_propose/create_plan + plan_apply_proposal to persist. |
| `agents.policy_preview` | `agents_run` | Expand AgentPolicy patterns to live MCP actions (preview, no persistence). |
| `agents.promote` | `agents_admin` | Run auto-promotion gates; may set state to live when metrics pass. |
| `agents.provider_preflight` | `mcp_read` | Read-only LLM provider snapshot for an agent before enqueue (quota hints, fallbacks). |
| `agents.reconcile_binding` | `mcp_read` | Validate and repair project orchestrator agent_uuid vs fleet index and lineage. |
| `agents.rollout_advance` | `agents_admin` | Advance canary rollout step (5→20→50→100). |
| `agents.run` | `agents_run` | Enqueue an agent run (durable work queue). instruction_slice and recovery_hint use context.locale / instruction_locale. |
| `agents.run_get` | `agents_run` | Get a run row plus normalized RunDetailDTO for cockpit/audit views. |
| `agents.run_resume_packet` | `mcp_read` | Cross-provider continuation: goal, step, packet, skills — no chat transcript. |
| `agents.run_with_agnt_credits` | `agents_run` | Demo orchestration: purchase compute credits (AGNT ledger) then enqueue an agent run. |
| `agents.runs_list` | `agents_run` | Rows are uuid, status, event_count, and timestamps. Event bodies stay on agents.run_get. Status and the event count are read in SQL. |
| `agents.skills_get` | `project_admin` | Fetch one skill card. Procedure text comes back whole until 64000 characters. truncated means chars is longer than text; that prefix is not the procedure to save. |
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
| `agents.work_next` | `mcp_read` | Agent-native next-work packet. Default detail=compact: state, next_action, reason, revision. panels is the string expand. Pass expand (array or comma string) to inline sections on this response only: |
| `agents.work_status` | `mcp_read` | Plan summary only: task counts and execution-ready leaves. The packet is small; the server still reads the plan leaf to count it. No node pick. |
| `agents.workflow_compile_preview` | `agents_run` | Validate workflow graph compile without persisting AgentSpec (read-only). |

## ai (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `ai.chat.settings.get` | `mcp_read` | Read project light-chat LLM settings for /chat and studio (surface light_chat). Returns settings (mode, limits.messages_per_user_day, on_limit, fallback_provider) and free_ai_available for the caller. |
| `ai.chat.settings.set` | `mcp_write` | Write project light-chat LLM policy: mode auto\|freeai\|own_key, limits.messages_per_user_day (0=unlimited), on_limit fallback\|manager\|stop, fallback_provider, max_tokens_per_reply. Owner/admin only (fo |
| `ai.providers.connect` | `project_admin` | Store an LLM provider API key in protected service_keys (ecosystem or project). Never writes secrets into treasury.credits. |
| `ai.providers.free_calls_set` | `project_admin` | Set ecosystem (or project) llm_free_calls policy — enable/prefer platform_supply providers (Free.ai) that fulfill builder_energy calls. |
| `ai.providers.list` | `mcp_read` | List LLM providers from the partner registry (openai/anthropic/gemini + OpenAI-compatible partners like freeai). Free.ai is platform_supply (debit_energy=true — users spend builder_energy). Includes d |
| `ai.providers.probe` | `mcp_read` | Lightweight LLM credential/quota probe for a provider (Agents preflight). |

## ai_builder (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `ai_builder.build.enqueue` | `mcp_read` | App Studio Vite queue only. AI Builder is off for shipping a game — do not use this. Put sources on the hosting journal and hosting.site.build. |
| `ai_builder.build.status` | `mcp_read` | Read a Vite build status, including file and line when it failed. |
| `ai_builder.compose.apply` | `project_admin` | Materialize a blueprint or UAM onto the project source tree. Does not run Vite. |
| `ai_builder.compose.preview` | `mcp_read` | Deterministic compose preview: returns content_sha256 and file paths. |
| `ai_builder.manifest.get` | `mcp_read` | Load Unified Application Manifest (UAM v1) for the current project. |
| `ai_builder.manifest.validate` | `mcp_read` | Validate a UAM v1 JSON object (structure + catalog + logic templates). |
| `ai_builder.publish` | `project_admin` | App Studio publish only. AI Builder is off for games — prod is hosting.git.promote_branch ref=dev target=prod, not this action. |
| `ai_builder.source.get` | `mcp_read` | Read one UTF-8 source file and its sha256. |
| `ai_builder.source.list` | `mcp_read` | List React source files under the project App Studio tree (src/** and related). Not hosting /s/ bucket bytes. |
| `ai_builder.source.patch` | `project_admin` | Patch one source file by replacements, a whole function, full content, or a unified diff. Never enqueues a build. Do not patch the published assets/index-*.js bundle. |
| `ai_builder.source.put` | `mcp_read` | Create or replace one source file. Does not start Vite. |

## analytics (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `analytics.archive.query` | `analytics` | Read-only cold archive rows (audit_logs + storage quota). Not included in analytics.project_snapshot. |
| `analytics.bump` | `analytics` | Increment analytics day-ring counters (catalog keys only: ae, ee, pe, rc, ec, eo, ev.*). Prefer organic writers; use for tests or manual corrections. |
| `analytics.events.list` | `analytics` | List product analytics events for a project (same rows as GET /api/analytics/events). Not included in analytics.project_snapshot. |
| `analytics.get_metrics` | `analytics` | Custom DNA metrics array (data.metrics[]) for a project — not performance KPIs. For dashboard KPIs use analytics.project_snapshot. |
| `analytics.get_usage` | `analytics` | Project activity usage slice (members + API/exec events). Prefer analytics.project_snapshot with include=['activity']. Date filters are accepted for forward-compat but not applied. |
| `analytics.platform_snapshot` | `analytics` | Ecosystem platform rollup (project_id=1) with platform_rollups slice. Alias of analytics.project_snapshot — prefer for operator platform KPIs. period is echoed on the envelope; platform_rollups uses f |
| `analytics.project_snapshot` | `analytics` | Unified project analytics snapshot: activity, finance, CRM, product events, optional business/bots/hosting slices. Prefer this over analytics.get_usage. |

## apikeys (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `apikeys.create` | `api_keys|apikeys.write` | Create an **agent PAT** (unified user PAT). Postel alias of `user.apikeys.create` with actor_kind=agent. |
| `apikeys.delete` | `api_keys|apikeys.write` | Delete an API key permanently. |
| `apikeys.list` | `api_keys|apikeys.read` | List personal and agent PATs (alias of user.apikeys.list). |

## assets (13)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `assets.activity` | `mcp_read` | Recent activity for one catalog asset (execution_ecosystem_logs; transfers and ops that set asset_id). Read-only. |
| `assets.bulk_delete` | `assets_write` | Delete multiple catalog assets in one write (REST bulk-delete parity). |
| `assets.bulk_update` | `assets_write` | Patch multiple catalog assets in one write (REST bulk-update parity). |
| `assets.create` | `assets_write` | Create a new asset for the project. |
| `assets.delete` | `assets_write` | Delete an asset from the project. |
| `assets.export` | `mcp_read` | Export all catalog rows from canonical assets.items[] (full records). |
| `assets.get` | `mcp_read` | Get asset details by ID. |
| `assets.import` | `assets_write` | Bulk import catalog assets from JSON (REST parity with POST .../assets/import). |
| `assets.list` | `mcp_read` | Page catalog assets (default projection=summary, limit 100, max 1000). |
| `assets.list_presets` | `mcp_read` | List deterministic asset wizard presets for the project. |
| `assets.reindex` | `assets_write` | Rebuild assets ECS index from canonical items[] (heal P6 list drift). |
| `assets.transfer` | `assets_write` | Transfer a catalog asset (inventory holding) to another user/project. |
| `assets.update` | `assets_write` | Update an existing asset. |

## auth (14)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `auth.anonymous.provision` | `mcp_read` | Provision an anonymous hosted-game player for project_id. Same outcome as POST /api/auth/anonymous/provision — ephemeral guest (eph_ask_*, no DNA user row). Returns api_key, user_id (1.8B+ band), acce |
| `auth.channel_identities.list` | `mcp_read` | List bot channel identities linked to the authenticated user profile. |
| `auth.channel_identities.revoke` | `project_admin` | Revoke a channel identity link by id (owner-only). |
| `auth.confirm_email.resend` | `mcp_read` | Resend signup email confirmation for an ecosystem user (enumeration-safe). Use when login returns email_unconfirmed and the original confirmation never arrived. |
| `auth.email_otp.login` | `—` | Verify the email code and mint a session. data.sessions[jti] survives a process restart. The SDK stores the bearer. Hosted flagship sends body.p. MFA: retry with mfa_ticket and mfa_code. |
| `auth.email_otp.send` | `—` | One-time sign-in code. purpose=login\|register\|convert. No auth.convert_anonymous_user tool. sent=true hides whether the account exists. needs_human: ask the human. UI: AuthWidget or <agentstack-auth>. |
| `auth.get_profile` | `—` | Get the session card for the current API key. |
| `auth.identity.conflicts` | `mcp_read` | List project member emails that diverge from ecosystem canonical auth email. |
| `auth.identity.resolve` | `mcp_read` | Resolve canonical vs display email for a user (support / debug). |
| `auth.login` | `—` | Email+password tenant sign-in — not Cursor MCP Connect. Passwordless: auth.email_otp.send → login. Session live: omit email → get_profile. |
| `auth.password_reset.request` | `—` | Request a self-service password reset email for an ecosystem user. Always returns the same success message (enumeration-safe). The user opens the link in the email, sets a new password, then signs in. |
| `auth.register` | `—` | New platform user (email+password). Passwordless: auth.email_otp.send purpose=register → login. Then auth.get_profile. |
| `auth.switch_project` | `—` | Rebind working project on the same identity session when mutation_allowed is false; for user-scoped Bearer prefer context.project_id / X-Project-ID without a new login. |
| `auth.update_profile` | `project_admin` | Update user profile. |

## bots (63)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `bots.acl.audit.list` | `bots_run` | Read recent group_access ECS audit after grant/revoke. |
| `bots.acl.chats.list` | `bots_run` | List indexed home-channel chat_keys for one bot (bot:provider:external_chat_id). |
| `bots.acl.get_mode` | `bots_run` | Read U15 group session mode. Pass conversation_id, or provider + external_chat_id. A hit only on the old raw chat-id leaf returns mode_source=legacy_external_chat_id (read-only; set_mode rewrites cano |
| `bots.acl.grant` | `bots_run` | Grant a messenger user on their AgentStack account for one project_id inside the grantor's membership. |
| `bots.acl.list` | `bots_run` | List group_access config for a home channel (DM, group, or supergroup). |
| `bots.acl.responsible.set` | `bots_run` | Set responsible_handle to a registered message-bus slug. One config.message_bus registry read per call; empty registry, unknown slug, or unreadable bus leaf → handle_not_found (fail closed). Same pred |
| `bots.acl.revoke` | `bots_run` | Revoke the messenger user's account grant (optional project_id_revoke drops one project). |
| `bots.acl.set_mode` | `bots_run` | U15: set group session mode consultation\|order. Pass conversation_id, or provider + external_chat_id so the server derives stable_conversation_id. Raw chat id is not the leaf key. Same leaf as Telegra |
| `bots.archive` | `bots_admin` | Archive a bot after go-live rollback or cleanup. |
| `bots.attach_channel` | `bots_admin` | Attach a project messaging connection (telegram, max, whatsapp, …) to a bot fleet row. Idempotent: re-attach same connection_uuid prunes stale Telegram channels without tokens. |
| `bots.broadcast` | `bots_admin` | Filtered mass outbound; same attachment envelope as bots.staff_reply (storage.upload → attachments[], no inline bytes; .md and archives as document). SMS and other no-media channels deliver files as l |
| `bots.broadcast_status` | `bots_run` | Read-only async broadcast progress after bots.broadcast returns fanout_job_id or plan_key. Surfaces coordinator job status, recipient_index vs plan_storage_ok recipient_total, and optional execute/out |
| `bots.bus.ack` | `bots_run` | Mark inbound turn acked (read) without outbound text. |
| `bots.bus.ask` | `bots_run` | Ask the person in this channel thread. Same keyboard as question on bots.bus.reply and bots.bus.send. Default ✅ Да / ❌ Нет. question {kind:choice, options:[...]} for several answers. |
| `bots.bus.deliver` | `bots_run` | Put text, attachments[], and/or task in another handle's mailbox. The sender is the caller's bound handle. Does not sign the message as the recipient. idempotency_key (or options.idempotency_key) make |
| `bots.bus.handle.list` | `bots_run` | List message-bus handles (config.message_bus.handles). |
| `bots.bus.handle.release` | `bots_run` | Release your @handle from config.message_bus.handles. |
| `bots.bus.handle.set` | `bots_run` | Register or replace your logical @handle for mailbox routing. |
| `bots.bus.health` | `bots_run` | Message-bus ops health: queues + channel capabilities. |
| `bots.bus.inbox` | `bots_run` | Alias: bots.conversations with reply_for (mailbox pull). With reply_for_scope=pending and include_messages=true, returns only unacked inbound turns for that handle — not the full session transcript. |
| `bots.bus.inbox_counts` | `bots_run` | Full inbox index counts for tab badges (handoff, needs_reply, agent_mailbox, …). |
| `bots.bus.onboard` | `bots_run` | Read-only agent message bus onboarding ladder (prompt, recipes, verify). |
| `bots.bus.platform_agent.get` | `bots_run` | Read platform_agent.enabled from message_bus DNA leaf. |
| `bots.bus.platform_agent.set` | `bots_run` | Set platform_agent.enabled on message_bus DNA leaf. |
| `bots.bus.reply` | `bots_run` | Quote-reply on bus rail (provider_message_id + signature). |
| `bots.bus.send` | `bots_run` | Send bus outbound text and/or attachments[] (signature + outbox) via staff_reply rail. |
| `bots.bus.settings.get` | `bots_run` | Read message-bus settings: signature_position, platform_agent, sticky_minutes. |
| `bots.bus.settings.set` | `bots_run` | Set signature_position on config.message_bus (prefix, suffix, or off). |
| `bots.bus.webhook.connect` | `bots_run` | Store outbound webhook connection for a handle (Integration Hub). |
| `bots.bus.webhook.disconnect` | `bots_run` | Clear the webhook leaf for a handle. Does not delete the Integration Hub connection. |
| `bots.bus.webhook.list` | `bots_run` | List message-bus webhook bindings (connection id, no secrets). |
| `bots.bus.webhook.rotate_secret` | `bots_run` | Rotate the webhook signing_secret. The new secret is returned once. |
| `bots.bus.webhook.status` | `bots_run` | One handle webhook: connected, url, has_authorization. No secret values. |
| `bots.bus.webhook.test` | `bots_run` | POST bus.message.inbound.test to the bound URL. |
| `bots.bus.whoami` | `bots_run` | Introspect message-bus handle ownership for the active MCP credential. |
| `bots.commands.upsert` | `bots_admin` | Merge one or more bot commands by trigger. Never replaces the command list. Use this instead of bots.update when editing /start, /help, or a single handler. |
| `bots.conversations` | `bots_run` | List bot conversations with handoff/needs_reply enrichment. include_messages returns the session turns the store already keeps (at most 200). |
| `bots.create` | `bots_admin` | Create a draft bot row (Telegram / WhatsApp / Instagram channel). Fleet shell on business head project_id; pass env_uuid on sandbox fleet steps after generation.fork. Use client_bot_key (tenant-assist |
| `bots.dlq_archive` | `bots_admin` | Cancel DLQ rows by last_error substring. Does not replay them. |
| `bots.dlq_list` | `bots_run` | List bot queue DLQ items before replay. Queue plane is production runtime — not sandbox 8DNA slice; home/env_uuid on fleet DNA tools do not apply here. |
| `bots.dlq_replay` | `bots_admin` | Replay one DLQ item after fixing root cause. |
| `bots.ensure_mentor_commands` | `bots_admin` | Heal mentor /start+/menu+/operator commands + inline buttons (idempotent). |
| `bots.fleet_diagnostics` | `bots_run` | Project bots fleet health (channel health, sessions) — project read RBAC. |
| `bots.flows.get` | `bots_run` | Load AgentFlowManifestV1 (declarative MCP block list) with optional routing_gates snapshot. Execute via bots.order_manager_bootstrap or recipe mcp_bot_order_manager_bootstrap_v1. |
| `bots.flows.validate` | `bots_run` | Validate AgentFlowManifestV1 (whitelist + DAG). Optional inline manifest or DNA load + vars substitution. |
| `bots.get` | `bots_run` | Load full agent_spec for edit or run. home=project \| self \| project_self — env_uuid only when home=project (sandbox overlay). owner=user for personal rows. Omit env for production read. |
| `bots.go_live` | `bots_admin` | Activate bot webhooks and set lifecycle live (Telegram / MAX / WhatsApp channels). |
| `bots.health` | `bots_run` | Channel health and channel_capabilities (media, buttons, session_window_hours) before staff_reply, broadcast, or commerce_offer attachments. media=false channels still accept files via storage.upload |
| `bots.list` | `bots_run` | Default projection=summary rows are uuid, title, deployment_state, and record_status. projection=full is bot_spec. |
| `bots.order_manager_bootstrap` | `bots_run` | Idempotent server runner for order_manager_v1: bus slug, responsible_handle, mode=order, verify routing_gates. Pass thread_id for forum topics (U15). |
| `bots.orders.get` | `bots_run` | Read one manager order by id (TG1692 C20). |
| `bots.orders.list` | `bots_run` | Read-model list of manager DM orders (TG1692 C20 projection). |
| `bots.pause` | `bots_admin` | Pause bot and optionally deactivate webhook. |
| `bots.reply_source.get` | `bots_run` | Read who answers a chat: ai (orchestrator, often Free.ai), manager (handoff), or bus agent. Workspace chats use bot_id=workspace and chat_key=workspace:{conversation_id}. When to use: before sending, |
| `bots.reply_source.set` | `bots_run` | Set who owns a chat. source=ai returns orchestration (use /release or set chat mode freeai). source=manager pairs with handoff so the bot brain stays quiet. source=agent requires agent_handle and rout |
| `bots.send_commerce_offer` | `bots_run` | Send a commerce offer card to a bot conversation (listing UUID → outbound attachment). |
| `bots.set_brain` | `bots_admin` | Wire Fleet agent or knowledge brain to the bot. Change one phrase in fallback_text with replacements, not a full paste. |
| `bots.set_handoff` | `bots_run` | Toggle handoff_active for a conversation; pair with bots.conversations to pick conversation_id. |
| `bots.simulate` | `bots_run` | Dry-run one bot brain turn without outbound. Heavy LLM: at most one per sync agentstack.execute (60s batch; 180s if this is the only heavy step). action=capabilities returns schema hints without LLM. |
| `bots.staff_reply` | `bots_run` | Enqueue staff/agent outbound to bots.outbound worker. MCP success is outbound_job_id only; use bots.conversations include_messages for provider_message_id and quote-reply index. |
| `bots.templates` | `bots_run` | List built-in bot templates before bots.create. |
| `bots.update` | `bots_admin` | Partial bot_spec update (commands, brain, name, …) — same as REST PUT. commands is a keyed list: pass write_mode (merge\|replace\|delete). A short commands array without write_mode merges by trigger and |
| `bots.waba_templates` | `bots_run` | List WhatsApp Business API templates (builtins + optional Graph sync). |

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

## business (14)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `business.apply_tariff` | `project_admin` | Apply a tariff template to a business head project. |
| `business.attach_child` | `project_admin` | Attach a child organ project to a business head. |
| `business.command_snapshot` | `mcp_read` | Business command center aggregate (finance + CRM + org). |
| `business.create_composite` | `project_admin` | Create business head + attach organ children from template. |
| `business.detach_child` | `project_admin` | Detach a child organ from business head. |
| `business.get_org` | `mcp_read` | Get business org config and children index for a head project. |
| `business.link_child` | `project_admin` | Link an existing owned project as a business organ (adopt). |
| `business.list_adopt_candidates` | `mcp_read` | List projects eligible for adopt/link into a business head. |
| `business.list_children` | `mcp_read` | List child organ projects for a business head. |
| `business.list_composite_adopt_candidates` | `mcp_read` | List standalone projects eligible for adopt during composite business create. |
| `business.list_tariff_templates` | `mcp_read` | List business tariff templates available for a head project. |
| `business.patch_org_settings` | `project_admin` | Patch business head org settings (config.org leaf). |
| `business.resolve_child` | `mcp_read` | Resolve child organ project_id by display_name under a business head. |
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

## channels (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `channels.create_bind_link` | `mcp_read` | Self-serve Telegram deep link (/start bind_*) for the signed-in user. |
| `channels.delete_source` | `mcp_read` | Remove a notification source by id. |
| `channels.deliver` | `mcp_read` | Send via unified channel plane (same bus as notifications.deliver). |
| `channels.delivery_snapshot` | `mcp_read` | Reachability snapshot: recent receipts, failed subset, and can_deliver flags (same as GET /users/me/notification-delivery-status). |
| `channels.delivery_status` | `mcp_read` | Read delivery receipts for a correlation_key from user DNA. |
| `channels.digest_depth` | `mcp_read` | Pending quiet-hours / digest_mode queue depth (same as GET /users/me/notify-digest-depth). |
| `channels.enroll_source` | `mcp_read` | Upsert a notification source (bot channel, web_push, email enrollment). |
| `channels.flush_digest` | `mcp_read` | Flush pending quiet-hours / digest notifications for the signed-in user. |
| `channels.list_event_triggers` | `mcp_read` | Catalog of normalized channel neural_event kinds for Logic subscriptions. |
| `channels.list_failed_deliveries` | `mcp_read` | Recent failed/skipped delivery receipts for the user. |
| `channels.list_identities` | `mcp_read` | List linked bot channel identities for the signed-in user. |
| `channels.resolve_routing` | `mcp_read` | Preview effective channels after prefs ∩ sources ∩ project policy. Reads those prefs on the server and does not return the profile. |
| `channels.route` | `mcp_read` | List, put, or remove one channel binding for the signed-in user. op=list returns the stored rows and the email plan. |
| `channels.setup` | `mcp_read` | Guided notification channel setup (recipe mcp_notify_channels_v1 ladder). |
| `channels.source_health` | `mcp_read` | Health for one enrolled notification source (REST GET /users/me/notification-sources/{source_id}/health). |
| `channels.sources_health` | `mcp_read` | Batch health for all enrolled notification sources (same as GET /users/me/notification-sources/health). |
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

## commerce (25)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commerce.ai.generate_products` | `payments` | Dry-run AI product generation — returns wizard answer dicts only. Mirrors POST /api/commerce/ai/generate-products. |
| `commerce.ai.magic_fill` | `payments` | Parse text into product wizard answers. Mirrors POST /api/commerce/ai/magic-fill. |
| `commerce.channel_pass.binding.get` | `mcp_read` | Read one ecosystem.commerce.channels.{binding_id} leaf (no full project blob). |
| `commerce.channel_pass.configure` | `project_admin` | Bind a Telegram channel for paid access: invite link after pay, ban when the pass expires. |
| `commerce.channel_pass.status` | `mcp_read` | Read channel pass entitlement for a buyer subject. |
| `commerce.channel_pass.sweep` | `project_admin` | Process channel_pass wake_index: expiry notices and Telegram revoke for lapsed access. |
| `commerce.checkout.session` | `payments` | Create a marketplace checkout session. Real money is not moved here. Confirm a wallet quote with finance.pay.execute. Card capture uses payments.create. Credits land on L0 project 1 or L2 treasury, ne |
| `commerce.coupon.create` | `payments` | Create a shop promo code (percent or fixed USDT off). Optional listing_uuids scopes the code to specific listings. Mirrors POST /api/commerce/merchant/coupons. |
| `commerce.coupon.delete` | `payments` | Delete a project promo code. Mirrors DELETE /api/commerce/merchant/coupons/{code}. |
| `commerce.coupon.list` | `payments` | List project-scoped promo codes from 8DNA commerce.coupon_registry. Mirrors GET /api/commerce/merchant/coupons. |
| `commerce.coupon.update` | `payments` | Update a project promo code. Mirrors PUT /api/commerce/merchant/coupons/{code}. |
| `commerce.refund.manual_complete` | `payments` | Ecosystem admin: mark fiat/manual refund compensated — revoke entitlements, set order refunded. Mirrors POST /api/admin/commerce/manual-refunds/complete. |
| `commerce.refund.manual_list` | `payments` | Ecosystem admin: list refund_requested orders awaiting manual compensation for a buyer commerce slice. Mirrors GET /api/admin/commerce/manual-refunds. |
| `commerce.refund.psp_refund` | `payments` | Ecosystem admin: run payments.refund for order.metadata.payment_id on payments_fiat rail (seller claw + Stripe/Tochka). Call before commerce.refund.manual_complete. |
| `commerce.refund.status` | `payments` | Poll refund request + compensation for an order. Mirrors GET /api/commerce/orders/{order_id}/refund-request. |
| `commerce.sell.activate` | `payments|project_admin` | One-shot seller activation: earnings wallet, product seed, public policy, storefront index upsert, optional hosted vitrine. Mirrors POST /api/commerce/sell/activate. |
| `commerce.storefront.health` | `payments` | Storefront index + ECS organelle health (admin diagnostics slice). |
| `commerce.storefront.hosted_manifest` | `payments` | Read hosted vitrine manifest (bucket, hosted_dirty, merchandising). Mirrors GET /api/commerce/storefront/hosted/manifest. |
| `commerce.storefront.hosted_publish` | `payments` | Publish hosted vitrine static bundle + tenant boot JSON when dist is on server. Mirrors POST /api/commerce/storefront/hosted/publish. |
| `commerce.storefront.list` | `payments` | List storefront listings via StorefrontReadFacade (ECS organelle). Mirrors GET /api/commerce/storefront/listings. Guest global marketplace: scope=global, type=auction, sort=ending. Filters: status=act |
| `commerce.storefront.one_click_fill` | `payments` | Create listings for eligible catalog assets without active listings. Mirrors POST /api/commerce/storefront/seed/one-click-fill. |
| `commerce.storefront.seed_apply` | `payments` | Apply a Storefront Studio seed plan (asset upsert → listing → index). Mirrors POST /api/commerce/storefront/seed/apply. |
| `commerce.storefront.seed_ingest` | `payments` | Parse bulk source (csv/json/ai_batch/magic/clone) into ProductSpec rows. Mirrors POST /api/commerce/storefront/seed/ingest. |
| `commerce.storefront.seed_plan` | `payments` | Dry-run Storefront Studio seed — compose asset/listing drafts and guidance hints. Mirrors POST /api/commerce/storefront/seed/plan. |
| `commerce.storefront.seed_undo` | `payments` | Undo a prior seed run — cancel listings and remove from storefront index. Mirrors POST /api/commerce/storefront/seed/undo. |

## commerce_rest (53)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rest.auth.hosted_app_session` | `—` | Mint hosted_app cookie after REST login — required before /s/{project_id}/mcp (not operator Bearer). |
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
| `rest.commerce.iap.balances` | `—` | Player soft-currency balances (parity wallets.balance). |
| `rest.commerce.iap.buyer_orders` | `—` | Buyer purchase history (parity shop.orders.list hosted scope). |
| `rest.commerce.iap.checkout` | `—` | Start hosted checkout (parity shop.checkout.create). |
| `rest.commerce.iap.checkout_resume` | `—` | Stripe Payment Element resume after ?iap_checkout=1 redirect (hosted cookie). |
| `rest.commerce.iap.enablement` | `—` | Visitor sell gate + blockers (parity shop.enablement.status; hosted cookie). |
| `rest.commerce.iap.entitlements` | `—` | Buyer IAP entitlements for hosted /s/{pid}/ (parity shop.entitlements.list). |
| `rest.commerce.iap.ledger` | `—` | Wallet ledger tail (parity wallets.ledger.list). |
| `rest.commerce.iap.order_cancel` | `—` | Cancel buyer pending_payment order (parity shop.orders.cancel). |
| `rest.commerce.iap.order_get` | `—` | Poll buyer order (parity shop.orders.get; hosted cookie). |
| `rest.commerce.iap.owner_enablement` | `—` | Owner sell gate + blockers (parity shop.enablement.status; project read). |
| `rest.commerce.iap.owner_orders` | `—` | Owner order tail; attention_only=1 filters grant_pending and metadata.fulfillment_phase=invites_pending plus fulfilled channel_pass invite gaps (parity shop.orders.list attention_only). |
| `rest.commerce.iap.payment_methods` | `—` | Checkout payment methods (parity payments.list_methods). |
| `rest.commerce.iap.products` | `—` | Visitor SKU catalog (parity shop.products.list). |
| `rest.commerce.iap.public_session` | `—` | Bot deep-link pay session without buyer cookie (parity shop.orders.get enrich; not payments.get). |
| `rest.commerce.iap.spend` | `—` | Spend soft currency in-game (parity wallets.spend). |
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
| `rest.commerce.scheduler.close_expired_auctions` | `—` | Worker batch: scan global auction index, authoritative DNA ends_at check, close_auction. Interval COMMERCE_CLOSE_AUCTIONS_INTERVAL_S (default 300s). |
| `rest.commerce.storefront.list_global` | `—` | Guest marketplace browse (TG 1810). Filters: project_id=seller, type, search, sort=ending. Prefer over GET /api/marketplace/listings for catalog. |
| `rest.exchange.execute` | `—` | Execute cross-project currency/asset exchange. |
| `rest.exchange.quote` | `—` | Cross-project exchange quote (not protein command_type exchange). |
| `rest.marketplace.accept_listing` | `—` | Accept deal / settle listing. X-Project-ID for buyer_project_id. 409 listing_has_active_deal for other buyer; same buyer 200 resumed. Terminal executed/cancelled deals allow re-accept. |
| `rest.marketplace.close_auction` | `—` | Seller or worker closes auction after ends_at. Bridges winning bid lock into deal escrow, confirms, execute_deal. REST-only (no MCP parity). |
| `rest.marketplace.create_listing` | `—` | Create listing (types: sale, buy, exchange, auction). Not an MCP action. |
| `rest.marketplace.list_bids` | `—` | Public bid ladder (bidder_label only). 404 when listing missing (not empty ladder). |
| `rest.marketplace.list_listings` | `—` | List marketplace listings with filters. |
| `rest.marketplace.place_bid` | `—` | Place bid on auction listing. Body: amount, currency (USD→USDT), optional expected_revision. Header Idempotency-Key (scoped; mismatch 409). X-Project-ID via resolve_commerce_project_id for wallet lane |
| `rest.projects.get_my_data` | `—` | Member-private project data read (hosted cloud save; MCP projects.get_my_data). |

## context (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `context.get` | `rag.read` | Prefetch grounded snippets before a reply or tool call that needs project knowledge. |
| `context.hint_use_params_project_id` | `mcp_read` | Instruction-only: there is no MCP context.set tool. Pass params.project_id on each execute step; bind context.project_id / X-Project-ID on the same jti when mutation_allowed is true — auth.switch_proj |
| `context.resolve` | `mcp_read` | Resolve effective project_id for the session; surfaces ambiguous_project_context. |

## crm (26)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `crm.create_company` | `crm` | Create a CRM company for a project. |
| `crm.create_deal` | `crm` | Create a CRM deal in the default pipeline. |
| `crm.delete_deal` | `crm` | Soft-delete (archive) a CRM deal. |
| `crm.erase_contact` | `crm` | Anonymize/erase a CRM contact (GDPR). |
| `crm.export_contact` | `crm` | Export GDPR bundle for a CRM contact. |
| `crm.get_contact_360` | `crm` | Contact 360 view: profile, deals, unified timeline. |
| `crm.get_crm_config` | `crm` | Project CRM config (saved_views, settings blob). |
| `crm.get_custom_fields` | `mcp_read` | Read CRM custom field definitions for a project. |
| `crm.get_deal_timeline` | `crm` | Activity timeline for a CRM deal (notes, tasks linked to deal_id). |
| `crm.hub_snapshot` | `crm` | CRM hub cold summary (deal/contact counts, pipeline hints) — index-only read. Same as REST GET /api/projects/{id}/crm/hub-snapshot. |
| `crm.import_contacts` | `crm` | Batch import CRM contacts with email dedupe. |
| `crm.index.repair` | `crm` | Backfill CRM deal_cards denorm rows from by_stage + deal entities. Use after legacy imports — list_board no longer runs this on every read. |
| `crm.list_board` | `crm` | Pipeline board. Default deals are cards: entity_id, title, status, updated_at. projection=full is the deal record. Stage columns stay. |
| `crm.list_companies` | `crm` | Default projection=summary rows are entity_id, kind, title, status, and updated_at. projection=full is the company record. |
| `crm.list_contacts` | `crm` | Default projection=summary rows are entity_id, kind, title, status, and updated_at. projection=full is the contact record. |
| `crm.log_activity` | `crm` | Log a CRM activity (note, call, task, etc.). |
| `crm.magic_fill` | `crm` | AI/heuristic autofill for quick-create (dry-run, no write). |
| `crm.merge_contacts` | `crm` | Merge duplicate contact into primary (absorb duplicate). |
| `crm.move_deal_stage` | `crm` | Move a deal to another pipeline stage. |
| `crm.patch_crm_config` | `crm` | Patch project CRM config (writers may update saved_views only). |
| `crm.put_custom_fields` | `crm` | Replace CRM custom field definitions (REST parity). |
| `crm.search` | `crm` | Search CRM contacts. Default projection=summary is the card (entity_id, kind, title, status, updated_at). projection=full is the hit record. |
| `crm.submit_public_form` | `crm` | Operator helper: submit a hosted-site lead (same shape as public form-intake REST). |
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

## diagnostics (9)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `diagnostics.consistency.scan` | `admin.read` | Scan integrity; set sync_plan=true to append findings as plan tasks (default read-only). |
| `diagnostics.gene_token_query` | `admin.read` | GTPI bool query against process-local gene token lexicon. |
| `diagnostics.neural_graph` | `admin.read` | Neural Visualizer Gen2 snapshot: topology, heat planes (protein/api/dna_query/gene), composition edges, hot_region_hints. Always includes heat_meta (worker layout honesty). Process-local heat; Tier F |
| `diagnostics.promote_hot_gene` | `admin.write` | Promote a genetic tag's path prefixes into HotProteinRegion for an entity. Modes: warm, pin, prefetch_only. Tier F materialize blocked. |
| `diagnostics.release_identity` | `mcp_read` | Platform MCP release stamps (instruction_revision, catalog etag, core version) — W8 drift probe without full consistency.scan. |
| `diagnostics.report_attach` | `diagnostics_report` | Append a follow-up note to an existing platform report. |
| `diagnostics.report_get` | `diagnostics_report` | Fetch one platform report by id (ECS body + attachments). |
| `diagnostics.report_list` | `diagnostics_report` | List platform problem reports (index rows). |
| `diagnostics.report_update` | `diagnostics_report` | Update platform report status (open, triage, resolved). |

## discovery (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `discovery.changelog` | `—` | MCP wire and Work Graph contract changes since a release tag (e.g. since=0.4.22). Cheap read for agent upgrade notes. |
| `discovery.describe` | `—` | Schema and instruction_slice for one action, in the request locale. Default descriptor is slim. descriptor=full attaches the runtime CapabilityDescriptor (inputSchema/outputSchema only when expand inc |
| `discovery.describe_many` | `—` | Slim discovery.describe for up to six canonical action ids in one read. Default descriptor=slim — same projection as discovery.describe per row. |
| `discovery.domain` | `—` | List actions in one catalog domain (wrapper over discovery.search). |
| `discovery.get_platform_surfaces` | `—` | List canonical platform routes from generated platform-surface-audit.json. |
| `discovery.instruction_get` | `—` | Fetch MCP instruction/playbook text by id (read-only). |
| `discovery.job_status` | `—` | Poll an async agentstack.execute job (options.async=true). Cheap. Use after a multi-step heavy batch; do not re-run bots.simulate to check status. |
| `discovery.list` | `—` | List MCP actions — default compact page (50 rows). Set full_catalog=true for the full GET /mcp/actions body (~900 tools). Includes execute_budget (sync 60s / one heavy LLM step) and write_modes (merge |
| `discovery.recipes` | `—` | List onboarding MCP recipes (instruction plane SoT). |
| `discovery.search` | `—` | Compact catalog search — returns ≤50 matching actions (not full discovery.list). Rows omit JSON schemas unless schemas_mode is set. Instruction text is one locale. |
| `discovery.status` | `—` | Registry status — version, catalog etag, action/domain/recipe totals. |
| `discovery.summary` | `—` | Lightweight MCP catalog totals (alias for GET /mcp/actions/summary). |

## dna (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `dna.lineage.get_ancestors` | `8dna.read` | Walk ancestor chain for an 8DNA row (materialized path or scoped CTE fallback). |
| `dna.lineage.get_descendants` | `8dna.read` | List descendant rows under a parent UUID within project scope. |

## docs (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `docs.analyze` | `mcp_read` | Validate a JSON payload against a registered docs.schema (structural check). |
| `docs.schema.define` | `mcp_write` | Register or update a versioned JSON schema document in project DNA. |
| `docs.schema.delete` | `mcp_write` | Remove a registered docs schema leaf from project DNA. |
| `docs.schema.list` | `mcp_read` | List schema ids registered under ecosystem.docs.schemas. |

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
| `energy.purchase` | `project_admin` | Buy an LLM energy pack from a wallet with finance.pay. Card is payments.create intent=energy_pack, then poll payments.get until wallet_credit=ok. |

## finance (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `finance.activity` | `mcp_read` | Unified wallet activity feed. |
| `finance.dashboard_bundle` | `mcp_read` | Finance hub landing aggregate (portfolio + billing_status + activity preview). MCP read model for /user\|/dev finance routes — not field-for-field with REST GET /api/billing/overview (SPA subscription |
| `finance.expense.create` | `project_admin` | Create an expense row on data.money.expenses[]. |
| `finance.expense.delete` | `project_admin` | Delete expense by id. |
| `finance.expense.list` | `mcp_read` | List project expenses on the canonical money.expenses leaf. Not inside a generation sandbox. Filter by date_from/date_to then limit. |
| `finance.pay.execute` | `payments` | Execute a quoted pay intent with idempotency key. Caller must own the quote. |
| `finance.pay.quote` | `mcp_read` | Quote a unified pay intent (energy_pack, subscription, compute_credits, stackscript_fuel_pack, fund, contribute). |
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

## games (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `games.leaderboards.submit` | `mcp_write` | Submit a score to an ecosystem.games.leaderboards board (server-side 8DNA leaf). |
| `games.leaderboards.top` | `mcp_read` | Read top scores for a leaderboard board (plain read). |

## generation (23)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `generation.approve` | `project_admin` | Mark manual generation approval on anchor and re-run gate evaluation. |
| `generation.archive` | `project_admin` | Archive an open sandbox anchor (draft, validating, or ready). Does not promote. cascade defaults false and only sets status=archived. |
| `generation.canary.abort` | `project_admin` | Abort canary rollout and restore previous production pointer. |
| `generation.canary.advance` | `project_admin` | Advance canary rollout to the next traffic weight step. |
| `generation.checkpoint` | `project_admin` | Materialise merged production/sandbox DNA into a checkpoint generation (rollback SoT). Use before risky bulk edits; rollback via generation.rollback. |
| `generation.diff` | `mcp_read` | Structured diff between two resolved generation entity states (same table/project). |
| `generation.diff_vs_prod` | `mcp_read` | Diff stable production head vs pending sandbox head. Includes agent_context summary for diagnosis (counts, domains, sample paths). instruction_slice and recovery_hint use context.locale / instruction_ |
| `generation.env_snapshot` | `mcp_read` | Lightest Gate A organ: one catalog read + hydrated anchors + sandbox_gate. No tier limits/usage SQL — prefer over generation.list for AgentStackSharp TG1611. |
| `generation.fork` | `project_admin` | Fork production project into a new sandbox environment generation. |
| `generation.gates` | `mcp_read` | Run generation gate chain for an environment (schema, conflict, health, soak, metrics, optional manual). |
| `generation.hosting.release` | `project_admin` | Snapshot live hosting workspace into an immutable release labeled with sandbox generation. Does not swap canonical /s/ URL — follow with hosting.release.promote. |
| `generation.list` | `mcp_read` | List sandbox environment anchors for a project (names, status, CalVer, auto_created flags). error_code list_unavailable means the read failed. |
| `generation.notify` | `project_admin` | Manual generation notify — webhooks, audit log, optional in-app notification. |
| `generation.pointer.set` | `project_admin` | Repair split-brain: set active_env_uuid without discarding sandbox content (TG1692 C09). Default dry_run=true. |
| `generation.promote` | `project_admin` | Promote a sandbox environment to production (runs promotion checks first). instruction_slice and recovery_hint use context.locale / instruction_locale. |
| `generation.queue` | `mcp_read` | Promotion queue: pending sandbox generations with gate evaluation (REST parity). |
| `generation.realign_to_prod` | `project_admin` | Revert pending sandbox DNA toward stable production by applying inverse diff (leaf jsonb_set / path delete on sandbox anchor — no full-blob wipe). Optional path_prefixes for partial revert. |
| `generation.rollback` | `project_admin` | Open a checkpoint as a new sandbox generation (draft). Promote the returned rollback_uuid via generation.promote to restore production DNA. |
| `generation.settings.get` | `mcp_read` | Read per-project auto_generation settings (REST parity: GET /api/generations/{id}/settings). |
| `generation.settings.patch` | `project_admin` | PATCH auto_generation settings on the production project slice. Enable auto_generation_mode so tracked MCP mutations auto-fork to sandbox. REST parity: PATCH /api/generations/{id}/settings. |
| `generation.status` | `mcp_read` | Lightweight generation + canary status for UI banners and agents. |
| `generation.supply_status` | `mcp_read` | Tenant generation supply snapshot: owner tier, open-env quota usage, auto_generation settings, pending/canary state. Alias for agents (same SoT as GET /api/sandbox/limits + generation.status). |
| `generation.timeline` | `mcp_read` | Promotion history for a project: completed, active canary (rolling_out), failed, and aborted requests (newest first). Items include strategy, traffic_weight, calver_label when recorded. |

## genetic (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `genetic.reindex` | `project_admin` | Rebuild genetic snapshot leaves from git journal paths. Respects generation sandbox when env_uuid is set or auto_generation forks active. |
| `genetic.search` | `mcp_read` | Search tenant genome nodes by tokenized query (tag, title, paths). |
| `genetic.snapshot.get` | `mcp_read` | Read derived genetic navigation graph for a project (ecosystem.genetic 8DNA leaves). Text SoT is in git; this is the cached graph for UI and agents. |

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

## hosting (63)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `hosting.bucket.file.get` | `mcp_read` | Read a bucket file. UTF-8 text is a window only past 64000 characters (offset). Base64 is the file read, max 2MB. |
| `hosting.bucket.file.import_url` | `project_admin` | Fetch one HTTPS asset and write it into a hosting bucket path (SSRF-safe). For dist/site ZIP or git repos use hosting.site.import_archive with source_url instead. |
| `hosting.bucket.file.patch` | `project_admin` | Edit one UTF-8 file. Pass bucket_id (project 1 has many sites; omit only for the primary). write_mode=search_replace + replacements [{find, replace}] for a small edit. write_mode=replace (alias conten |
| `hosting.bucket.file.rename` | `project_admin` | Rename or move a file within a bucket. |
| `hosting.bucket.files.bulk_delete` | `project_admin` | Delete multiple files by path list or prefix. |
| `hosting.bucket.files.list` | `mcp_read` | List files in a hosting bucket (optional prefix, cursor pagination, optional q path substring filter). |
| `hosting.bucket.grep` | `mcp_read` | Search UTF-8 bucket text files for a substring or regex. Optional glob (e.g. *.html) and prefix narrow the scan. Returns truncated=true when max_files or max_matches hit — tighten glob/prefix first. |
| `hosting.bucket.preview_publish` | `project_admin` | Copy live bucket tree to preview plane and refresh preview symlink. |
| `hosting.bucket.publish` | `project_admin` | Publish live /s/ bytes for a bucket (gates + manifest + edge health). |
| `hosting.content.patch` | `project_admin` | Patch content/site.yaml (or other YAML path) via structured path/value writes. |
| `hosting.demo.status` | `mcp_read` | Public demo hosting pool status (enabled, depth, sandbox project id). |
| `hosting.demo_store.status` | `mcp_read` | Golden promo demo-store vitrine: catalog row count on sandbox, published build id, manifest bucket (`frontend.commerce.hosted_storefront.gen1`). |
| `hosting.deploy_files` | `project_admin` | Batch-upload files to a bucket and optionally publish (max 50 files per call, 2MiB inline per file). Large trees: chunk with finalize=false (server returns upload_session_id), then repeat with same up |
| `hosting.domain.connect` | `project_admin` | Alias of hosting.site.settings.patch for mount_apex_hosts on an already-served host. |
| `hosting.files.put` | `project_admin` | Upload or replace one file in a hosting bucket. Many files: one hosting.deploy_files call. List: hosting.bucket.files.list. |
| `hosting.gc.ix` | `mcp_read` | Prune orphan hosting_buckets/_ix symlinks (admin diagnostics broken_ix). |
| `hosting.gc.orphans` | `project_admin` | Remove abandoned _gen staging dirs older than TTL (dry_run default). |
| `hosting.git.init` | `project_admin` | Init platform git on the protected source folder. Branches dev and main. Post-receive enqueues preview on push to dev and production on push to main — poll hosting.site.build_status; do not assume URL |
| `hosting.git.log` | `mcp_read` | Paginated git log (main\|dev). Returns task_ids from AgentStack-Task trailers (Work Graph node id). Public changelog.json is the player feed. |
| `hosting.git.migrate` | `project_admin` | Repair AgentGit journal/worktree/hooks for legacy hosting buckets. |
| `hosting.git.promote_branch` | `project_admin` | Land any journal branch on dev (preview) or fast-forward main for production. target=dev merges ref onto refs/heads/dev and enqueues the preview build. target=prod requires the ref already on dev, fas |
| `hosting.git.pull_upstream` | `project_admin` | Fast-forward optional upstream into platform git. Refuses a dirty tree. Token is not stored in the site leaf. Then build target=dev yourself. |
| `hosting.git.status` | `mcp_read` | Git status of the protected source tree. |
| `hosting.project.set_primary_site` | `project_admin` | Set the project's primary public site URL when multiple sites exist (PH-18). |
| `hosting.project.status` | `mcp_read` | Project hosting plane: Host/Sell/Scale ladder, sites summary, next_actions. |
| `hosting.quota.breakdown` | `mcp_read` | Read-only hosting quota components (live, staging _gen, workspace, archives). |
| `hosting.quota.recount` | `project_admin` | Reconcile DNA hosting quota with on-disk measure (owner write for apply). |
| `hosting.release.clone_site` | `project_admin` | Clone a new site from a release snapshot. |
| `hosting.release.delete` | `project_admin` | Delete an unpinned hosting release (optional bucket purge). |
| `hosting.release.diff` | `mcp_read` | Path-level manifest diff between two immutable release trees. |
| `hosting.release.download` | `mcp_read` | Release byte manifest + TTL metadata (bulk zip via REST when enabled). |
| `hosting.release.get` | `mcp_read` | Fetch one hosting release row by release_uuid. |
| `hosting.release.list` | `mcp_read` | List site release history (newest first). |
| `hosting.release.patch` | `project_admin` | Update release metadata (label and/or pinned flag). |
| `hosting.release.promote` | `project_admin` | Promote a release snapshot to the live site bucket and publish. |
| `hosting.release.snapshot` | `project_admin` | Create immutable snapshot (archive bucket copy) for a site. |
| `hosting.site.build` | `project_admin` | Run site.build.yaml in bucket workspace into staging; default async. Before compile, .gitmodules URLs under agentstack.tech/api/git/ are symlinked to the canonical _sources journal. This step does not |
| `hosting.site.build_cancel` | `project_admin` | Cancel a pending or running hosting.site.build job. |
| `hosting.site.build_status` | `mcp_read` | Poll async hosting.site.build job by job_id. |
| `hosting.site.delete` | `project_admin` | Remove a hosted site bucket (same as REST DELETE /api/hosting/buckets/{bucket_id}). |
| `hosting.site.edge_health` | `mcp_read` | Check nginx edge readiness (symlinks, index.html) for a hosting bucket. |
| `hosting.site.import_archive` | `project_admin` | Import hosted site bytes: ZIP dist publishes to live (optional publish); ZIP with package.json without dist/ enqueues workspace build on --dev (poll build_status). HTTPS git URL (GitHub/GitLab, not .z |
| `hosting.site.preset.apply` | `project_admin` | Apply recommended edge/header preset (vite_pwa, spa, static). |
| `hosting.site.prune` | `project_admin` | Prune stale hashed assets not in last K releases (dry_run default). |
| `hosting.site.purge` | `project_admin` | Invalidate edge/CDN cache for site paths (origin no-op when no CDN). |
| `hosting.site.quick_start` | `project_admin` | Create bucket, upload HTML index, optional publish; returns public /s/{project_id}/{bucket_name}/ URL. instruction_slice and recovery_hint use context.locale / instruction_locale. |
| `hosting.site.resolve` | `mcp_read` | Resolve hosting bucket_name to bucket_id and public URL hints. |
| `hosting.site.retention.set` | `project_admin` | Set per-project archive release count cap (DNA quota.releases_max_per_site). |
| `hosting.site.settings.patch` | `project_admin` | Patch mount_apex_hosts, csp_profile, and git upstream on one site. This is the same-edge domain connection (hosting.domain.connect). |
| `hosting.site.upload_grant` | `project_admin` | Mint a one-time POST grant for multipart ZIP upload (no bytes in MCP JSON). Agent HTTP client POSTs form field file to /api/hosting/ingress/upload with header X-Ingress-Grant. |
| `hosting.storage.import_folder` | `project_admin` | Import a project storage folder into a hosting bucket. |
| `hosting.surface.resolve` | `mcp_read` | Read-only deploy surface router: map intent to recipe_id (agents). |
| `hosting.sync.commit` | `project_admin` | Finalize upload_session staging and optionally publish (after hosting.deploy_files chunks). |
| `hosting.sync.plan` | `mcp_read` | Server manifest (path, sha256, bytes) for client-side hash diff before batched upload. |
| `hosting.verify` | `mcp_read` | Verify live/preview hosting health (alias of hosting.site.edge_health; intent taxonomy edge_health). |
| `hosting.visual.inspect` | `mcp_read` | Capture viewport screenshots for visual QA (375 and 1440). Requires preview_url. |
| `hosting.workspace.file.delete` | `project_admin` | Delete a source file from bucket workspace/ (not live current). |
| `hosting.workspace.file.get` | `mcp_read` | Read a source file from bucket workspace/ (edit tree, not live /s/). |
| `hosting.workspace.file.put` | `project_admin` | Write a source file into bucket workspace/. Publish with hosting.site.build. |
| `hosting.workspace.files.list` | `mcp_read` | List source files in bucket workspace/ (not the live current tree). |
| `hosting.workspace.import_tree` | `project_admin` | Copy an allowlisted source directory into the bucket workspace. Does not publish. hosting.deploy_files writes live bytes, not this tree. |
| `hosting.workspace.init` | `project_admin` | Prepare bucket workspace/ (the tree hosting.site.build compiles). seed_from_live copies live only when index.html is absent. mirror_from_live overwrites workspace copies from the live site so a later |
| `hosting.workspace.meta` | `mcp_read` | Per-bucket workspace init flag and sources_sha (no full project.status scan). |

## integrations (63)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `integrations.activate_webhook` | `mcp_read` | Register provider inbound webhook for a connection (post-OAuth or manual retry). Telegram: sets BotFather webhook URL to this connection's hook slug. |
| `integrations.connection_health` | `mcp_read` | Read connection health snapshot (public spec only). |
| `integrations.connector_schema` | `mcp_read` | Provider setup schema (required secrets, setup_steps, quick_setup) — call before install_recipe or create_connection so agents know which secrets= keys to pass. |
| `integrations.create_connection` | `project_admin` | Create an integration connection (REST POST /api/integrations/connections parity). Prefer integrations.install_recipe for packaged inbound webhooks. |
| `integrations.create_scenario` | `mcp_read` | Create a draft integration scenario with backing logic rule. |
| `integrations.delete_connection` | `mcp_read` | Delete an integration connection and unregister webhook bindings. |
| `integrations.diagnose_telegram` | `mcp_read` | Read-only Telegram connection health: token, webhook_secret, public URL, getWebhookInfo. |
| `integrations.dynamic_fields` | `mcp_read` | List dynamic field sources for an integration app (schema-only in Phase 1). |
| `integrations.export_connection` | `mcp_read` | Export connection public spec + config (secrets redacted). |
| `integrations.export_connection_manifest` | `mcp_read` | Export project integration connections as JSON manifest for agent tool loops. |
| `integrations.export_scenario` | `mcp_read` | Export portable scenario bundle (rule document + field maps, secrets redacted). |
| `integrations.generate_scenario` | `mcp_read` | Keyword-only RuleDocumentV1 draft (no LLM). Server sets preview_only=true. Continue with integrations.save_scenario_draft then logic.dry_run before publish. |
| `integrations.generate_signing_secret` | `project_admin` | Rotate signing_secret server-side without reading the old value. |
| `integrations.get_app` | `mcp_read` | Get a single integration app definition by provider id. |
| `integrations.get_connection` | `mcp_read` | Get integration connection metadata (secrets redacted). |
| `integrations.get_diagnostics` | `mcp_read` | Integration Hub diagnostics rollup and worker flags. |
| `integrations.get_hubspot_relay_stats` | `mcp_read` | HubSpot platform relay rollup (7d success/denied/no-connection/missing portal). |
| `integrations.get_scenario` | `mcp_read` | Get integration scenario detail (draft + publish metadata). |
| `integrations.hydrate_triggers` | `mcp_read` | Hydrate integration trigger registry from connection DNA (cold start recovery). |
| `integrations.import_connection` | `project_admin` | Import connection from export JSON (optionally with secrets). |
| `integrations.import_make_commit` | `integrations:write` | Commit Make scenario JSON → installed connection + logic (not dry-run). |
| `integrations.import_make_preview` | `mcp_read` | Dry-run preview: map Make scenario JSON to recipe + logic block draft. |
| `integrations.import_n8n_commit` | `integrations:write` | Commit n8n workflow JSON → installed connection + logic (not dry-run). |
| `integrations.import_n8n_preview` | `mcp_read` | Preview n8n workflow JSON as AgentStack steps. Does not install or run it. |
| `integrations.import_zapier_commit` | `integrations:write` | Commit Zapier export JSON → installed connection + logic (not dry-run). |
| `integrations.import_zapier_preview` | `mcp_read` | Dry-run preview: map Zapier export JSON to recipe + logic block draft. |
| `integrations.install_recipe` | `project_admin` | Install a recipe: creates connection + declarative logic rule. |
| `integrations.list_app_actions` | `mcp_read` | List action definitions for an integration app provider. |
| `integrations.list_app_triggers` | `mcp_read` | List trigger definitions for an integration app provider. |
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
| `integrations.mailbox` | `mcp_read` | Read or send mail on a connected Gmail or Microsoft mailbox. op is list, get, or send. Tokens stay on the connection. list returns id, subject, from, snippet (max 10). get returns plain text whole unt |
| `integrations.migrate_legacy_webhooks` | `mcp_read` | Copy projects.config.webhooks into integration_connection rows. |
| `integrations.oauth_begin` | `mcp_read` | Start OAuth 2.0 PKCE flow for an integration connection (returns authorization_url). |
| `integrations.poll_connection` | `mcp_read` | Run connector polling for a connection (cursor watermark in config.poll_state). |
| `integrations.preview_scenario_maps` | `mcp_read` | Evaluate scenario field maps against a sample payload (secrets redacted). |
| `integrations.process_inbox_batch` | `mcp_read` | Process durable inbound integration inbox (worker-style batch). |
| `integrations.process_outbound_batch` | `mcp_read` | Process outbound webhook delivery work queue (webhooks.outbound batch). |
| `integrations.process_polling_batch` | `mcp_read` | Poll due integration connections (config.poll_enabled, cursor watermark). |
| `integrations.publish_scenario` | `mcp_read` | Publish scenario (live) and enable backing logic. |
| `integrations.rebind_triggers` | `mcp_read` | Re-sync enabled logic rule triggers for a project or all connection projects. |
| `integrations.recipe_taxonomies` | `mcp_read` | Facet lists for integration recipes (provider, category, tags). Use before integrations.list_recipes to build filters — same payload as GET /api/integrations/recipes/taxonomies. |
| `integrations.refresh_token` | `mcp_read` | Refresh OAuth access token for a connection when expired (protected storage). |
| `integrations.register_module` | `mcp_read` | Register a reusable sub-scenario module stub on a parent connection. |
| `integrations.replay_delivery` | `mcp_read` | Replay outbound delivery by durable delivery_id (execution log). |
| `integrations.replay_delivery_url` | `mcp_read` | Replay outbound POST to an explicit target_url (same as REST deliveries/replay query). |
| `integrations.replay_inbox` | `mcp_read` | Replay a durable inbox event through the test-hook intake path. |
| `integrations.reveal_secrets` | `mcp_read` | Reveal connection secrets for operators (audit-logged; high privilege). |
| `integrations.rotate_secret` | `project_admin` | Rotate a protected secret key for a connection. |
| `integrations.save_scenario_draft` | `mcp_read` | Save scenario draft (spec and/or logic patch). Parity with REST POST /scenarios/{id}/draft. |
| `integrations.social_post` | `mcp_write` | Publish, thread, delete, or draft on a connected X account (provider=x). OAuth connection required. Prefer over raw http_fetch. Not for user alerts (notifications.deliver) or TG chat (bots.staff_reply |
| `integrations.test_hook` | `mcp_read` | Dry-run inbound hook verify + normalize (sync dispatch to logic). |
| `integrations.test_scenario_step` | `mcp_read` | Dry-run a scenario step via logic simulation. |
| `integrations.unpublish_scenario` | `mcp_read` | Unpublish scenario (pause) and disable backing logic. Parity with REST unpublish. |
| `integrations.update_connection` | `mcp_read` | Patch integration connection metadata (label, status, config, direction). |

## knowledge (43)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `knowledge.acceptance.case.run` | `bots_run` | Run one acceptance case; eval_passed mirrors harness ok. |
| `knowledge.acceptance.case.upsert` | `bots_run` | Append regression case to config.knowledge.acceptance_cases (merge, not replace). |
| `knowledge.acceptance.list` | `bots_run` | List built-in acceptance fixture suites before knowledge.acceptance.run. |
| `knowledge.acceptance.run` | `bots_run` | Run built-in or tenant acceptance suite; gate on data.eval_passed. |
| `knowledge.access.grant` | `bots_run` | Grant product/tier access to a principal user (entitlement row). |
| `knowledge.access.list` | `bots_run` | List access grants before grant/revoke or entitlement QA. |
| `knowledge.access.revoke` | `bots_run` | Revoke a principal product grant after refund or role change. |
| `knowledge.chunk.patch` | `bots_run` | Patch one indexed chunk by chunk doc_id; re-ingests the parent document. search_replace / unified_diff apply to the full joined body (same editor as content.patch). REST: PATCH /knowledge/kb/chunks/{d |
| `knowledge.config.get` | `bots_run` | GET knowledge config — RAG lanes, entitlements, default_playground_bot_uuid, ingest policy. |
| `knowledge.config.patch` | `bots_run` | Patch knowledge DNA config + safety playbooks (same as PATCH /knowledge/config). gene_pack merges by name; safety_playbooks default write_mode is patch (per-playbook overlay — other playbook ids are k |
| `knowledge.content.delete` | `bots_run` | Deactivate or hard-delete a KB document by source_doc_id. Same as DELETE /knowledge/content/{id}. |
| `knowledge.content.get` | `bots_run` | Get one KB document by source_doc_id. text is a character window only past the compact envelope (64000). Concatenate windows before a replace. One window is not the document. text_chars is the full jo |
| `knowledge.content.list` | `bots_run` | Page operator KB documents (default 50). Each item includes title, source_doc_id, chunk_count, and joined text from this fetch (at most 2000 chunks), not an unbounded document. |
| `knowledge.content.patch` | `bots_run` | Patch a KB document: metadata (title, gene_stems, …) and/or body. Body write_mode is replace, append, prepend, search_replace, or unified_diff (fuzzy, same editor as App Studio source.patch). Same as |
| `knowledge.correction.propose` | `bots_run` | Draft correction steps from a journal turn — NO auto-write. |
| `knowledge.doctor.run` | `bots_run` | Read-only mentor doctor for any project (gene pack + retrieval probe). |
| `knowledge.eval.retrieval` | `bots_run` | Retrieval fixture eval — parity with mentor.eval.retrieval and REST POST /eval/retrieval. |
| `knowledge.eval.run` | `bots_run` | Golden safety eval cases against live KnowledgeConfig. |
| `knowledge.eval.run_all` | `bots_run` | Full platform eval harness (safety + retrieval + readback). |
| `knowledge.export` | `bots_run` | Export knowledge config + KB snapshot for backup. A fat bundle returns shape.mode outline and no bodies. detail=full raises the ceiling. |
| `knowledge.gc.reconcile` | `bots_run` | Enqueue principal/GetCourse GC reconcile (async heavy job). |
| `knowledge.gc.status` | `bots_run` | GC queue status before reconcile or bulk ingest cleanup. |
| `knowledge.gene_pack.pinned_shape.upsert` | `bots_run` | Merge one pinned_shapes binding (phenotype → glossary_stem) + compile_warnings. |
| `knowledge.health` | `bots_run` | Knowledge plane health snapshot before ingest or playground. instruction_slice and recovery_hint use context.locale / instruction_locale. |
| `knowledge.import` | `bots_run` | Import knowledge pack JSON into project scope. |
| `knowledge.journal.get` | `bots_run` | Fetch one mentor journal turn by correlation_id or turn_id for diagnose/correction. |
| `knowledge.journal.list` | `bots_run` | Mentor journal turns. Default 50, server max 200. No cursor. |
| `knowledge.kb.heal_index` | `bots_run` | Realign DNA storage_ref.sha256 to on-disk sqlite for a KB collection (no wipe). Fixes rag_index_sha256_mismatch. Default collection_id=content_registry. |
| `knowledge.kb.ingest` | `bots_run` | Ingest documents into a KB collection (async by default). Failures attach instruction_slice and recovery_hint in the request locale. |
| `knowledge.phenotype.upsert` | `bots_run` | Single FAQ route / gene_pack row — merge by name; prefer over full gene_pack patch. Then knowledge.retrieve.probe; promote via generation.* when auto_generation_mode on. |
| `knowledge.playground` | `bots_run` | Full bot simulate with RAG/safety/gene-lock trace (REST playground). Heavy LLM: one per sync agentstack.execute. Prefer over raw rag.search for mentor fidelity. Same budget as bots.simulate. instructi |
| `knowledge.policy_templates.apply` | `bots_run` | Apply a platform policy template onto the tenant: plan_instructions append (marker [policy:id]), merge crisis match_any (keep tenant template), union gene_pack.situational_needles, optional safety_fai |
| `knowledge.policy_templates.list` | `bots_run` | Platform policy templates (synthesis / crisis / situational needles / gate). Same catalog as GET /knowledge/policy-templates. Apply writes 8DNA via existing prompt+config PATCH — not a second bus. |
| `knowledge.policy_templates.status` | `bots_run` | Read-only policy snapshot: synthesis [policy:id] markers in plan_instructions, playbook ids, gate mode, situational needle count. Same as GET /knowledge/policy-templates/status. |
| `knowledge.principal.simulate` | `bots_run` | Read effective tier and context.product_ids before knowledge.playground — no LLM. Optional product_id simulates paid-SKU gate downgrade. |
| `knowledge.project_ai_keys.patch` | `bots_run` | Patch tenant project AI keys in project.protected (KeySelector). Same storage as PUT /api/projects/{id}/ai-keys — no secret values in GET. |
| `knowledge.project_ai_keys.status` | `bots_run` | AI key presence + ai_settings flags (no secret values). |
| `knowledge.project_secrets.apply` | `bots_run` | Apply Unity tenant secrets: OpenAI → project.protected; GetCourse/MAX/Telegram → Integration Hub protected (telegram → wire_unity_telegram). Pass openai/getcourse/max/telegram[/telegram2] and optional |
| `knowledge.prompt.get` | `bots_run` | Grounded assistant preamble + plan_instructions (same as GET /knowledge/prompt). Both strings come back whole. A field over the compact envelope is a preview, not the field to patch. system_preamble_c |
| `knowledge.prompt.patch` | `bots_run` | Patch grounded assistant system_preamble and/or plan_instructions (same as PATCH /knowledge/prompt). omit-unset keeps unsent keys; a sent field is the full string. Writes prompt_settings leaf via patc |
| `knowledge.registry.list` | `bots_run` | List content registry entries before upsert. |
| `knowledge.registry.upsert` | `bots_run` | Upsert registry metadata for FAQ/guide documents. |
| `knowledge.retrieve.probe` | `bots_run` | Hybrid retrieval probe (no LLM) — top doc_ids for a query. |

## logic (19)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `logic.attach_template` | `logic_write` | Install a template from the built-in catalog into the current project. |
| `logic.create` | `logic_write` | Create new Logic Engine rule for project. |
| `logic.delete` | `logic_write` | Delete a Logic Engine rule. |
| `logic.diff_versions` | `mcp_read` | Diff two rule versions. Returns delta only. A long old or new value is an envelope preview, not the leaf. Roll back with logic.restore_version. |
| `logic.dry_run` | `logic_write` | Simulate a logic rule execution without side effects (F2.5). |
| `logic.execute` | `logic_write` | Execute a Logic Engine rule immediately. |
| `logic.export_json` | `mcp_read` | Export a logic rule as JSON for backup / cross-project copy. |
| `logic.flush_batch` | `logic_write` | Force flush logic batch for a project (saves all accumulated rules immediately) |
| `logic.get` | `mcp_read` | Get detailed information about a Logic Engine rule. |
| `logic.get_commands` | `mcp_read` | Get list of available commands for triggers. |
| `logic.get_processors` | `mcp_read` | Get list of available processors (same as processors.list but in logic context). |
| `logic.import_json` | `logic_write` | Import a logic rule from JSON (creates a new rule). |
| `logic.install_blueprint` | `logic_write` | Install a catalog blueprint into the project (alias of attach_template with field_values). |
| `logic.list` | `mcp_read` | Page Logic Engine rules for a project (default 100, max 1000). |
| `logic.list_versions` | `mcp_read` | List historical snapshots stored on a rule (F2.9). |
| `logic.mcp_actions_catalog` | `mcp_read` | List MCP actions addressable from logic rules. |
| `logic.restore_version` | `logic_write` | Roll a rule back to a prior snapshot (F2.9). |
| `logic.signals_catalog` | `mcp_read` | List signal channels the logic engine can subscribe to. |
| `logic.update` | `logic_write` | Update existing Logic Engine rule. |

## mentor (8)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `mentor.eval.retrieval` | `bots_run` | Retrieval slice eval before knowledge.eval.run_all. |
| `mentor.eval.run` | `bots_run` | Run single mentor eval — prefer knowledge.eval.run. |
| `mentor.eval.run_all` | `bots_run` | Full eval harness — prefer knowledge.eval.run_all. |
| `mentor.export` | `bots_run` | Export mentor config — prefer knowledge.export. |
| `mentor.journal.list` | `bots_run` | Mentor turn journal — prefer knowledge.journal.list. |
| `mentor.kb.ingest` | `bots_run` | Ingest KB for mentor — prefer knowledge.kb.ingest. |
| `mentor.principal_link.bind_code` | `bots_run` | Bind principal/GetCourse link code to user. |
| `mentor.principal_link.upsert` | `bots_run` | Upsert principal link row after bind_code. |

## messaging (16)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `messaging.get_auth_email_readiness` | `social_read` | Probe auth email plane — provider secrets, email_confirmation + password_reset + email_otp templates. |
| `messaging.inbox.get` | `social_read` | Read one thread as text for the signed-in user. messages are the oldest 50. |
| `messaging.inbox.list` | `social_read` | List inbox cards for the signed-in user. Bodies are not included. |
| `messaging.inbox.patch` | `social_read` | Mark one inbox thread read or unread for the signed-in user. |
| `messaging.inbox.reply` | `social_read` | Reply to the latest noticed letter. A connected Gmail or Microsoft mailbox sends when that letter has a connection id. Otherwise the letter goes out from the signed-in @agentstack.tech address. |
| `messaging.list_suppressions` | `social_read` | List ecosystem email suppression list (bounces/complaints). |
| `messaging.mail.send` | `social_read` | Send one letter from the signed-in @agentstack.tech address. |
| `messaging.mailbox.agent.set` | `social_read` | Save or clear the agent id on the signed-in mailbox. A later letter queues that agent. This call does not start a run. |
| `messaging.mailbox.alias.add` | `social_read` | Add one alias on the signed-in mailbox. Cap is 5. |
| `messaging.mailbox.alias.remove` | `social_read` | Remove one alias from the signed-in mailbox. |
| `messaging.mailbox.claim` | `social_read` | Claim local@agentstack.tech for the signed-in user. |
| `messaging.mailbox.get` | `social_read` | Read the signed-in user's @agentstack.tech address, aliases, and notify toggles. Only the mailbox leaf is read. The rest of the profile stays in Postgres. |
| `messaging.mailbox.settings.patch` | `social_read` | Patch notify toggles on the signed-in mailbox: forward_to_canonical, notify_in_app, notify_push, notify_bots. |
| `messaging.project_mailbox.claim` | `project_admin` | Claim local@agentstack.tech on the project slice (8DNA data.mailbox). REST parity: POST /api/projects/{project_id}/mail/mailbox. Requires project_admin; recipients receive fanout copies in their user |
| `messaging.project_mailbox.get` | `project_read` | Read the project's shared @agentstack.tech address from the project 8DNA mailbox leaf. REST parity: GET /api/projects/{project_id}/mail/mailbox. |
| `messaging.send_email` | `social_read` | Send email to any address (ecosystem admin). Use template_name + template_data for auth templates. |

## notifications (12)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `notifications.cancel_push` | `mcp_read` | Cancel pending push items by correlation key prefix. |
| `notifications.delete_source` | `mcp_read` | Remove a notification source by id (parity with DELETE notification-sources). |
| `notifications.deliver` | `mcp_read` | Send one notice on email, in-app, web push, webhook, or a bound bot channel (telegram, max, whatsapp, instagram, slack, discord, sms). |
| `notifications.inbox_list` | `mcp_read` | List Fabric in-app inbox rows for a project (parity with GET /api/notifications/{project_id}). |
| `notifications.job_status` | `mcp_read` | Status of one outbound or deliver work item by job_id. |
| `notifications.list_prefs` | `mcp_read` | Messenger prefs plus category×channel matrix and enrolled bot sources. |
| `notifications.mark_read` | `mcp_read` | Mark one project inbox notification as read (parity with PATCH …/read). |
| `notifications.register_category` | `mcp_read` | Register a logical push category in user messenger prefs (persisted). |
| `notifications.send_push` | `mcp_read` | Enqueue OS web push for a user (self or admin). |
| `notifications.send_with_fallback` | `mcp_read` | Deliver with category fallback chain (security/auth/billing) and in_app escalation when all channels fail. Prefs are read on the server; the profile is not returned. |
| `notifications.subscribe_push` | `mcp_read` | Returns VAPID status; browser must still call PushManager.subscribe. |
| `notifications.update_prefs` | `project_admin` | Patch messenger prefs and/or category×channel notification matrix. One channel: patch {category, channel, enabled} calls set_category_channel and does not replace the rest of the matrix. work_graph + |

## operators (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `operators.destructive_consent.begin` | `mcp_read` | Register a human gate for a destructive MCP step. Pair with bots.bus.ask (gate_id in message). After owner taps Yes, call operators.destructive_consent.grant then re-execute the destructive action wit |
| `operators.destructive_consent.grant` | `mcp_read` | Mark owner destructive consent granted after Telegram bus Yes. |

## organelle (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `organelle.execute` | `mcp_read` | Invoke a registered organelle op via dispatch (ring_pool / cell_store today; storage & work_queue via related tools). |

## payments (8)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `payments.admin.keys_preflight` | `payments` | Ecosystem-only: probe stored AgentPay PSP keys (Stripe Balance.retrieve, Tochka configured). |
| `payments.admin.put_keys` | `payments` | Ecosystem-only: merge Stripe/Tochka secrets into AgentPay protected.keys.payments. |
| `payments.create` | `payments` | Create a new payment transaction. |
| `payments.get` | `payments` | Payment row by payment_id — status, wallet_credit, soft_currency_grant (IAP L1), amount/currency. Hosted /s/ game buyers: shop.orders.get + checkout resume, not this tool. |
| `payments.get_balance` | `payments` | Get current wallet balance for a project. |
| `payments.list_methods` | `mcp_read` | Region-aware checkout methods (card, Pix probe) for hosted IAP UI. Empty methods = no PSP for that currency. |
| `payments.list_transactions` | `payments` | List all payment transactions for a project. |
| `payments.refund` | `payments` | Reverse only after payments.get shows wallet_credit=ok. Debit that lane before any card provider. |

## permissions (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `permissions.effective` | `mcp_read` | Effective permission surface: token caps ∩ RBAC role ∩ buff limits. Optional resource + path_prefixes for Field Access Policy write preflight. |

## platform (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `platform.fleet.apply_operator_preset` | `agents_admin` | Apply ecosystem operator preset to project orchestrator agent policies. |
| `platform.fleet.get_operator_preset` | `agents_admin` | Load ecosystem operator MCP tool policy preset (ecosystem admin). |
| `platform.fleet.set_operator_preset` | `agents_admin` | Save ecosystem operator MCP tool policy preset. |

## preflight (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `preflight.check` | `—` | Tenant mutation preflight — supply, limits, optional RBAC permission. instruction_slice follows the request locale. |
| `preflight.simulate` | `mcp_read` | IAM-style dry-run: would this session be allowed to call MCP action X? Returns L1 caps, L2 RBAC, and optional FAP path probe. |

## processors (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `processors.execute` | `logic_write` | Execute a processor directly. |
| `processors.get_metadata` | `mcp_read` | Get detailed metadata for a specific processor. |
| `processors.list` | `mcp_read` | List all available processors with their metadata. |

## profile (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `profile.spotlight.move` | `mcp_read` | Point a personal Spotlight/VIP/premium beam at an owned standalone Free project. REST twin: POST /api/profile/spotlight. Not projects.patch_my_data. |

## projects (35)

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
| `projects.get_data` | `mcp_read` | Read project.data without pulling a fat leaf. A path under the byte budget returns that value. A thicker node returns an outline, a key page, an item page, or a text window. detail=full asks for the b |
| `projects.get_deletion_inventory` | `mcp_read` | Pre-delete inventory: blockers, warnings, and execution_token for safe delete. |
| `projects.get_my_data` | `mcp_read` | Read the signed-in member's project data (REST GET /projects/{id}/users/me/data). A path under the byte budget returns that leaf. A thicker node returns an outline or a page. Omit path for top-level n |
| `projects.get_notify_policy` | `mcp_read` | Read project.data.notify_policy (channel plane tenant defaults). |
| `projects.get_project` | `mcp_read` | Get one project. |
| `projects.get_projects` | `mcp_read` | List workspaces for the signed-in user (summary or full projection, filters, pagination). See when_to_use and list_meta.read_model (ecs_cards, accessible_ids, membership_scan). |
| `projects.get_stats` | `mcp_read` | Get project statistics. |
| `projects.get_users` | `mcp_read` | Get list of users in a project. |
| `projects.import_by_key` | `project_admin` | Import accessible projects using a user API key (operator-sensitive). |
| `projects.join` | `mcp_write` | Join an open community project as the authenticated session user. |
| `projects.leave` | `mcp_read` | Leave a project as the authenticated session user (self-remove membership). |
| `projects.limits.get` | `mcp_read` | Project effective limits — buffs + bot create gate metadata. |
| `projects.membership.repair` | `project_admin` | Repair project membership / accessible_ids lag for the caller. |
| `projects.orchestrator.export_pack` | `mcp_read` | Export portable ProjectOrchestratorPack JSON. |
| `projects.orchestrator.get` | `mcp_read` | Get project_orchestrator 8DNA leaf config. |
| `projects.orchestrator.import_from_asset` | `project_admin` | Fulfill orchestrator pack from commerce asset or raw pack dict. |
| `projects.orchestrator.patch` | `project_admin` | Patch project_orchestrator leaf (dna_set_project_data_path). |
| `projects.patch_data` | `project_admin` | Leaf SET on project.data (REST PATCH /projects/{id}/data). write_mode=replace sets the path; merge overlays an object; delete removes it. Or ops (≤16) on one path: merge, search_replace, insert_before |
| `projects.patch_my_data` | `project_admin` | Leaf write on the signed-in member's project data (REST PATCH /projects/{id}/users/me/data). write_mode replace sets the path; merge overlays an object. String leaves: value={replacements} or unified_ |
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
| `rag.document_list` | `rag.read` | List document chunks in a collection (default 50, max 1000, same as REST). vector_compressed is omitted. A long text is an envelope preview, not the chunk. |
| `rag.export` | `rag.read` | Export accessible collection chunks (default 5000), access-tier filtered. A long text is an envelope preview, not the chunk. |
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

## rooms (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rooms.create` | `mcp_write` | Create a custom lobby (host seat). Signed-in players only — guests use quick_match or join_by_code. |
| `rooms.join_by_code` | `mcp_write` | Join a lobby by invite_code from rooms.create (guest-friendly). |
| `rooms.leave` | `mcp_write` | Leave a quick-match seat; empty matches close (TTL GC also applies). |
| `rooms.list_open` | `mcp_read` | List public waiting lobbies (no private invite-only rooms). |
| `rooms.match_status` | `mcp_read` | Read quick-match status by match_id. |
| `rooms.poll_events` | `mcp_read` | Poll match events (player_seated, bot_seated, started, player_left). Also soft-triggers bot fill when bot_fill_after_ms elapsed. WS fallback plane. |
| `rooms.quick_match` | `mcp_write` | Atomic find-or-create match seat for hosted games (1v1/2v2/ffa4). Idempotent via idempotency_key. Bot-fill after bot_fill_after_ms or fill_with_bot. |

## safety (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `safety.evaluate` | `bots_run` | Score user or model text against the project safety policy and return a verdict. |

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

## script (20)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `script.archive` | `stackscript_write` | Archive a script index entry (reversible via script.put index patch in sandbox). Does not delete SSBC versions — use script.delete for tombstone. |
| `script.delete` | `stackscript_admin` | Tombstone script index (destructive). SSBC blobs may remain for audit — prefer script.archive when retiring from Studio. |
| `script.docs` | `stackscript_read` | StackScript language docs (TypeScript subset). Topics: syntax, overview, fuel, migration, host, runtime, room, idempotency, dna_repair, limits (aliases: rooms, multiplayer). |
| `script.dry_run` | `stackscript_read` | Execute without persisting side effects. |
| `script.get` | `stackscript_read` | Get script metadata and version info. |
| `script.list` | `stackscript_read` | List scripts in project. |
| `script.profile` | `stackscript_read` | Execution aggregates and hot lines. |
| `script.publish` | `stackscript_admin` | Move alias live/staging to a version. |
| `script.put` | `stackscript_write` | Compile and store StackScript sources (TypeScript .ts subset → SSBC). files[]: {path, content} with TS source — not JSON logic rules. validate_only=true: compile receipt only (no DNA version). Persist |
| `script.recompile_version` | `stackscript_write` | Re-compile retained sources for a version; refresh SSBC blob and meta.exports (heal legacy entry_pc without script.put bump). |
| `script.rollback` | `stackscript_admin` | Rollback live alias to a prior version. |
| `script.room.create` | `mcp_write` | Create in-process StackScript room (MCP script.room.create — not a TS defineRoom export). |
| `script.room.join` | `mcp_write` | Join player to in-process room table. |
| `script.room.leave` | `mcp_write` | Leave in-process StackScript room seat; empty rooms close. |
| `script.room.status` | `mcp_read` | Read in-process StackScript room runtime state (script.room.* table; no defineRoom TS API). |
| `script.room.tick` | `mcp_write` | Advance one room tick (≤20 Hz) and commit bound defineRoom export (idempotency room:{pid}:{room_name}:{tick}). |
| `script.run` | `stackscript_run` | Execute a published script export on production DNA. Batch generation_env_uuid is ignored; use script.dry_run in sandbox. |
| `script.studio_bundle` | `stackscript_read` | Studio read model: detail files + version ladder in one call. |
| `script.test` | `stackscript_write` | Smoke-test an export via dry_run VM (no DNA commit). Optional expect_value assertion. |
| `script.versions` | `stackscript_read` | List versions and aliases for a script. |

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

## shop (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `shop.audit.list` | `project_admin` | Owner: recent IAP audit_log events (products upsert, checkout, cancel, refund) from DNA leaf ecosystem.commerce.iap.audit_log. |
| `shop.checkout.create` | `payments` | Start visitor checkout for a SKU (server price; no client amount). |
| `shop.embed.snippet` | `mcp_read` | HTML embed snippet from commerce-surface-snippets fixture for agents. |
| `shop.enablement.status` | `mcp_read` | Read-only sell gate: live checkout vs sandbox-only blockers. |
| `shop.entitlements.list` | `mcp_read` | List buyer entitlements for hosted IAP (REST parity). |
| `shop.fulfill` | `service_internal` | System: fulfill paid order (idempotent). |
| `shop.orders.cancel` | `payments` | Cancel stuck pending_payment IAP order (buyer-owned). |
| `shop.orders.get` | `mcp_read` | Poll IAP order status after checkout redirect. |
| `shop.orders.list` | `mcp_read` | Visitor purchase history (hosted, scoped user_id) — rows include terminal, channel_pass_invites_pending, recovery_hint (buyer-safe). Merchant support tail: attention_only=true (project_admin, omit use |
| `shop.orders.reconcile_stale` | `payments` | Fail stale pending_payment orders without a PSP checkout session (PAY-01). |
| `shop.products.list` | `mcp_read` | List active IAP SKUs for visitors or all for owner. |
| `shop.products.upsert` | `project_admin` | Create or update an in-app SKU (server-priced IAP pack). |
| `shop.refund` | `project_admin` | Owner refund fulfilled/paid/grant_pending with optional clawback (default true). Composite channel_pass SKUs shorten access first; response includes clawback map and channel_pass_legs when applicable. |
| `shop.reports.summary` | `project_admin` | Owner: gross/refunds rollup from commerce_iap_orders. |
| `shop.sandbox.simulate` | `project_admin` | Simulate checkout+fulfill in sandbox mode (no PSP charge). |
| `shop.sinks.upsert` | `project_admin` | Owner: configure spend sinks (cost vectors) for wallets.spend. |
| `shop.webhooks.subscribe` | `project_admin` | Owner: HMAC outbound shop.order.* events to your backend. |

## showcase (20)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `showcase.arcanestack.ensure_rules` | `project_admin` | Leaf-sync card-game FAP and logic templates onto the catalog proof project. Does not publish SPA bytes — those come from the git journal. |
| `showcase.build.republish_entry` | `project_admin` | Republish one platform_proof entry (ops_jurisdiction=showcase_bootstrap, PIDs 1438–1443). Not for tenant_generation_supply — use agentstack_safe_project_cycle. |
| `showcase.catalog.apply_canonical_order` | `project_admin` | Rewrite ecosystem DNA entry_order so hosted games lead (TailRush first) without changing card payloads — matches /showcase SPA sort. |
| `showcase.catalog.delete_entry` | `project_admin` | Remove one gallery card from ecosystem DNA (project_id=1). Drops entries_by_id, entry_order, and showcase.projects slug map. Does not delete tenant hosting bytes — use generation supply on tenant pid. |
| `showcase.catalog.diff_vs_fixture` | `mcp_read` | Drift report: DNA catalog entry ids vs git fixture. |
| `showcase.catalog.export_fixture` | `mcp_read` | Export merged catalog as fixture-shaped JSON for git sync. |
| `showcase.catalog.get` | `mcp_read` | Merged public showcase catalog (ecosystem DNA + fixture seed). Each catalog.entries[] row includes icon_path and icon_candidates (svg/png/webp/jpg fallback chain under /showcase/icons/). Prefer showca |
| `showcase.catalog.list_entries` | `mcp_read` | Lightweight list of showcase catalog cards (merged). Each row includes icon_path + icon_candidates (svg→png→webp→jpg) for gallery and MCP agents. |
| `showcase.catalog.patch_entry` | `project_admin` | Partial update of one merged gallery card on ecosystem DNA (project_id=1). Uses effective catalog (DNA + fixture fill) as base. Patch keys match ShowcaseCatalogEntry fields (title, tagline, hosting_bu |
| `showcase.catalog.reorder` | `project_admin` | Set gallery card order (entry_order on ecosystem DNA). Only ids present in DNA entries_by_id are kept; unknown ids are dropped silently. |
| `showcase.catalog.sync_fixture` | `project_admin` | Pull git fixture showcase_catalog_v1.json into ecosystem DNA (pid=1) — no SPA deploy. mode=missing (default): add cards absent from DNA. mode=align: also overwrite shared ids from fixture (hosting_buc |
| `showcase.catalog.upsert_entry` | `project_admin` | Upsert one full ShowcaseCatalogEntry on ecosystem DNA (pid=1). Validates schema (project_slug showcase-*, active+hosting_bucket needs project_id>2). Prefer patch_entry for copy/bucket tweaks; upsert f |
| `showcase.entry.resolve` | `mcp_read` | Route a showcase catalog entry_id to operator plane and suggested MCP actions (platform_proof vs independent_tenant). |
| `showcase.health.probe` | `project_admin` | HTTP-probe canonical /s/{pid}/ buckets for active catalog entries and stamp ecosystem health when edge returns ok. Pass entry_ids to scope probes; omitting probes all active entries with hosting_bucke |
| `showcase.project.heal_gallery_marks` | `project_admin` | Seed tenant ``showcase.icon_url`` when no real gallery mark exists — uses the same candidate order as the public BFF (monogram before unprobed /s/…/favicon). Skips tenants that already have HTTPS, sto |
| `showcase.settings.patch` | `project_admin` | Patch showcase gallery settings on ecosystem DNA. |
| `showcase.sources.sync` | `project_admin` | Operator contract for VPS showcase-sources sync. SCP runs via Deploy Console step deploy.showcase_sync_sources (Invoke-ShowcaseSyncSourcesOnVps), not from API. |
| `showcase.sources.sync_status` | `project_admin` | Read VPS showcase-sources sync sidecar (sha256) when present. |
| `showcase.tenant.register` | `project_admin` | Register an independent tenant on the public showcase (gallery card only). |
| `showcase.workspace.hydrate` | `project_admin` | Copy platform_proof archetype sources into bucket workspace/ for MCP edit + site.build. |

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
| `social.chat.crdt_state` | `social_read` | Read the CRDT snapshot and update log for a channel. |
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
| `social.merge_feed` | `social_read` | Merge read of multiple channel pointers (reader must access each). Each pointer is the latest 200 messages. Page one channel with social.chat.history. |
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

## stackscript (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `stackscript.dna.replay` | `stackscript_admin` | Replay pending write_set from a dna_repair DNA leaf after script.run commit_failed. |
| `stackscript.fuel.purchase` | `stackscript_run` | Apply a quoted StackScript fuel pack to project prepaid balance. |
| `stackscript.fuel.quote` | `stackscript_read` | Quote a StackScript fuel top-up pack (prepaid VM fuel, not LLM compute credits). |
| `stackscript.wallet.replay` | `stackscript_admin` | Replay pending wallet_ops from a wallet_repair DNA leaf after script.run wallet_commit_failed. |

## storage (14)

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
| `storage.import_from_url` | `storage.write` | Server-side HTTPS fetch into ecosystem storage (SSRF-safe, project write cap). Site trees and ZIP dist: hosting.site.import_archive or hosting.bucket.file.import_url. Max 10 MiB per call — same order |
| `storage.list_by_entity` | `storage.read` | List files bound to an entity_ref. |
| `storage.list_files` | `storage.read` | Lists files in an ecosystem storage folder for the current user or project slice. Pass folder key (use '_' for root) or omit to list all folders (heavy). Set include_folders=1 to return folder_index f |
| `storage.reconcile` | `storage.read` | Project RBAC: fix disk↔8DNA drift for the resolved row (dry_run default true). scope=user requires write; scope=project requires owner/admin. Prefer over storage.admin_reconcile for tenants. |
| `storage.scan` | `storage.read` | Project RBAC: compare on-disk files with 8DNA storage registry for the resolved row. scope=user (default) scans caller member row; scope=project scans project slice (user_id=0). Prefer over storage.ad |
| `storage.upload` | `storage.read` | Binary upload uses REST POST /api/storage/upload (multipart/form-data). This MCP action returns the contract + quota gate — call storage.get_quota first. |

## stream (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `stream.relay.create_room` | `mcp_write` | Create a stream relay room (DNA ecosystem.stream_relay.rooms) for multiplayer hosts. |
| `stream.relay.list_rooms` | `social_read` | List stream relay rooms for the active project (same service as REST GET /api/stream-relay/rooms). |
| `stream.relay.ws_ticket` | `mcp_read` | Mint a short-lived JWT for wss://…/api/stream-relay/ws/rooms/{room_id}?token=… Do not put API keys in the WS query. Polling fallback: rooms.poll_events. |

## system (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `system.ping` | `—` | Health check tool — always succeeds, no authentication required. |
| `system.version` | `mcp_read` | Platform core version string (deploy probes; prefer over removed legacy aliases). |

## time (3)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `time.list` | `—` | List recent time entries. |
| `time.log` | `—` | Log a time entry (minutes) optionally linked to crm_deal. |
| `time.report` | `—` | Aggregate time report for project. |

## trust (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `trust.check` | `mcp_read` | Read-only agent trust surface (metrics + optional ERC-8004 reputation rollup). |
| `trust.feedback` | `mcp_write` | Record L0 reputation feedback for an on-chain agent id (sybil: one row per receipt). |

## user (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `user.apikeys.create` | `project_admin` | Create a personal AgentStack PAT on the signed-in user (ecosystem identity). |
| `user.apikeys.list` | `mcp_read` | List personal PATs (metadata only). Optional project_id filters agent keys scoped to that project. |
| `user.apikeys.revoke` | `project_admin` | Revoke (soft-delete) a personal PAT by key_id. |
| `user.apikeys.rotate` | `project_admin` | Rotate a personal PAT; returns a new secret once. |
| `user.apikeys.update` | `project_admin` | Update PAT metadata (name and/or status). Caps change requires rotate or create. |

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

## vision (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `vision.ask` | `mcp_write` | Ask human operator a vision question (notification + optional agents.approve_run). |
| `vision.extract` | `mcp_read` | Extract structured fields (product_photo.v1) from an inspected asset. |
| `vision.inspect` | `mcp_read` | Inspect one image (metrics envelope). storage_id preferred over https. |
| `vision.pipeline.run` | `mcp_write` | Run a named vision pipeline (dry_run recommended). |
| `vision.trace.get` | `mcp_read` | Return vision pipeline trace steps. |

## wallets (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `wallets.balance` | `mcp_read` | Player soft-currency balances (commerce_iap ledger), not AgentPay wallets.list. Hosted: prefer GET /api/commerce/iap/balances or sdk getBalances. |
| `wallets.create` | `payments` | Create a wallet for a project or user. |
| `wallets.deposit` | `payments` | Deposit funds to a wallet. |
| `wallets.ledger.list` | `mcp_read` | Append-only IAP ledger entries for the signed-in player. Hosted: GET /api/commerce/iap/ledger or sdk listIapLedger. |
| `wallets.list` | `payments` | List wallets for a project (and optionally user). |
| `wallets.spend` | `payments` | Spend at configured sink; idempotency_key required. Hosted: POST /api/commerce/iap/spend or sdk spendAtIapSink. |
| `wallets.transfer` | `payments` | Transfer funds between wallets (same project). |

## web_push (1)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `web_push.get_health` | `mcp_read` | Per-user Web Push health (same payload as GET /api/push/health): VAPID, canDeliver, subscription/outbox counts, process-local worker stats. |

<!-- END:AUTOGEN-CAPABILITY-MATRIX -->

## References

- `docs/plugins/CONTEXT_FOR_AI.md` — intent routing by domain.
- `docs/adr/PUBLIC_DOCS_CLASSIFICATION.md` — audience policy.
