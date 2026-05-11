# AgentStack MCP Capability Matrix

> **Auto-generated.** Do not edit by hand — regenerate via the AgentStack repository capability-matrix script or CI.

- Source: in-process `mcp.routes._build_mcp_actions_catalog_payload` (set `MCP_ACTIONS_SOURCE=live` for `GET https://agentstack.tech/mcp/actions` first)
- Generated: 2026-05-09 09:21 UTC
- Total actions: **226**
- Gene: `repo.plugins.capability_routing.gen1`

<!-- BEGIN:AUTOGEN-CAPABILITY-MATRIX -->

## agents (17)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `agents.create` | `agents_admin` | Create a new agent row (draft AgentSpec). |
| `agents.create_from_template` | `—` | Create an agent row from a built-in template (atomic merge on server). |
| `agents.delete` | `agents_admin` | Delete an agent row. |
| `agents.fork` | `agents_admin` | Fork an agent to a new generation (DNA row). |
| `agents.gates` | `agents_run` | Evaluate promotion gates for an agent. |
| `agents.get` | `agents_run` | Get a single agent by UUID (default owner=project; personal rows need owner=user). |
| `agents.kill` | `agents_admin` | Set agent lifecycle state to killed (hard stop for new runs). |
| `agents.list` | `agents_run` | List Agents Fleet rows for a project (8DNA entity_type=agent). |
| `agents.metrics` | `agents_run` | Read rollup metrics for an agent (ecosystem.agents_metrics). |
| `agents.promote` | `agents_admin` | Run auto-promotion gates; may set state to live when metrics pass. |
| `agents.rollout_advance` | `agents_admin` | Advance canary rollout step (5→20→50→100). |
| `agents.run` | `agents_run` | Enqueue an agent run (durable work queue). |
| `agents.stop` | `agents_run` | Cancel a running / queued agent run. |
| `agents.template_preview` | `—` | Return merged AgentSpec-shaped preview for a template (no persistence). |
| `agents.templates_list` | `—` | List built-in Agent Fleet templates (canonical Python catalog). |
| `agents.traces` | `agents_run` | Return stored run events (trace buffer) for a run. |
| `agents.update` | `agents_admin` | Replace AgentSpec for an agent (full document). |

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

## assets (5)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `assets.create` | `assets_write` | Create a new asset for the project. |
| `assets.delete` | `assets_write` | Delete an asset from the project. |
| `assets.get` | `—` | Get asset details by ID. |
| `assets.list` | `—` | List all assets in the project. |
| `assets.update` | `assets_write` | Update an existing asset. |

## auth (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `auth.get_profile` | `—` | Get user profile information. |
| `auth.login` | `—` | Login to the system. |
| `auth.register` | `—` | Register a new user. |
| `auth.update_profile` | `—` | Update user profile. |

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

## commands (2)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `commands.execute` | `—` | Execute a single Protein Command via universal API. |
| `commands.execute_batch` | `—` | Execute multiple Protein Commands in batch. |

## commerce_rest (7)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rest.exchange.execute` | `—` | Execute cross-project currency/asset exchange. |
| `rest.exchange.quote` | `—` | Cross-project exchange quote (not protein command_type exchange). |
| `rest.marketplace.accept_listing` | `—` | Accept deal / settle listing (may trigger internal payment flow). |
| `rest.marketplace.close_auction` | `—` | Close auction listing. |
| `rest.marketplace.create_listing` | `—` | Create listing (types: sale, buy, exchange, auction). Not an MCP action. |
| `rest.marketplace.list_listings` | `—` | List marketplace listings with filters. |
| `rest.marketplace.place_bid` | `—` | Place bid on auction listing. |

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

## generation (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `generation.approve` | `—` | Mark manual generation approval on anchor and re-run gate evaluation. |
| `generation.diff` | `—` | Structured diff between two resolved generation entity states (same table/project). |
| `generation.gates` | `—` | Run generation gate chain for an environment (schema, conflict, health, soak, metrics, optional manual). |
| `generation.list` | `—` | List sandbox environment anchors for a project (names, status, CalVer, auto_created flags). |
| `generation.promote` | `—` | Promote a sandbox environment to production (runs promotion checks first). |
| `generation.timeline` | `—` | Recent completed promotion requests for a project (newest first). |

## logic (18)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `logic.attach_template` | `—` | Install a template from the built-in catalog into the current project. |
| `logic.create` | `logic_write` | Create new Logic Engine rule for project. |
| `logic.delete` | `logic_write` | Delete a Logic Engine rule. |
| `logic.diff_versions` | `—` | Diff two rule snapshots (F2.9). |
| `logic.dry_run` | `—` | Simulate a logic rule execution without side effects (F2.5). |
| `logic.execute` | `logic_write` | Execute a Logic Engine rule immediately. |
| `logic.export_json` | `—` | Export a logic rule as JSON for backup / cross-project copy. |
| `logic.flush_batch` | `logic_write` | Force flush logic batch for a project (saves all accumulated rules immediately) |
| `logic.get` | `—` | Get detailed information about a Logic Engine rule. |
| `logic.get_commands` | `—` | Get list of available commands for triggers. |
| `logic.get_processors` | `—` | Get list of available processors (same as processors.list but in logic context). |
| `logic.import_json` | `—` | Import a logic rule from JSON (creates a new rule). |
| `logic.list` | `—` | List all Logic Engine rules for project. |
| `logic.list_versions` | `—` | List historical snapshots stored on a rule (F2.9). |
| `logic.mcp_actions_catalog` | `—` | List MCP actions addressable from logic rules. |
| `logic.restore_version` | `—` | Roll a rule back to a prior snapshot (F2.9). |
| `logic.signals_catalog` | `—` | List signal channels the logic engine can subscribe to. |
| `logic.update` | `logic_write` | Update existing Logic Engine rule. |

## notifications (6)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `notifications.cancel_push` | `—` | Cancel pending push items by correlation key prefix. |
| `notifications.list_prefs` | `—` | Messenger + web push preference snapshot (minimal). |
| `notifications.register_category` | `—` | Register a logical push category in user messenger prefs (persisted). |
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

## projects (11)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `projects.add_user` | `project_admin` | Add a user to a project (requires Professional subscription or ecosystem context and manage_users/owner). |
| `projects.create_project` | `—` | Create a new project. |
| `projects.create_project_anonymous` | `—` | Create a new anonymous project (for AI agents). |
| `projects.delete_project` | `project_admin` | Delete a project. |
| `projects.get_project` | `—` | Get detailed information about a specific project. |
| `projects.get_projects` | `—` | Get list of projects for the current user. |
| `projects.get_stats` | `—` | Get project statistics. |
| `projects.get_users` | `—` | Get list of users in a project. |
| `projects.remove_user` | `project_admin` | Remove a user from a project (requires Professional subscription or ecosystem context). |
| `projects.update_project` | `project_admin` | Update an existing project. |
| `projects.update_user_role` | `project_admin` | Update a user's role in a project (requires manage_users or owner; cannot assign owner via this tool). |

## rag (10)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `rag.collection_create` | `—` | Create a new RAG knowledge base collection for the current project. |
| `rag.collection_delete` | `—` | Delete a RAG collection and all its documents. This is irreversible. |
| `rag.collection_list` | `—` | List all RAG collections for the current project. |
| `rag.document_add` | `—` | Add a text document to a RAG collection. |
| `rag.document_delete` | `—` | Remove a document (all its chunks) from a collection by source_doc_id. |
| `rag.document_list` | `—` | List document chunks stored in a collection. |
| `rag.memory_add` | `—` | Store a conversation turn in AI memory. |
| `rag.memory_get` | `—` | Get the most recent conversation turns from memory (chronological order). |
| `rag.memory_search` | `—` | Semantically search past conversation turns. |
| `rag.search` | `—` | Semantic search over a RAG collection. |

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

## social (79)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `social.admin.channel_search` | `—` | Operator: search channel metadata on a home_project_id by substring (id/title). |
| `social.admin.chat_index_page` | `—` | Operator: paginated messenger chat index for a user (cursor/limit). |
| `social.admin.chat_sweep` | `—` | Run one chat history prune batch. Requires ecosystem owner/admin on project 1. |
| `social.admin.directory_search` | `—` | Operator: unified typeahead — users (8DNA) + channels for a home_project_id. |
| `social.admin.dm_thread_meta` | `—` | Operator: DM thread metadata (counts, participants) for support; audited. |
| `social.admin.force_close_channel` | `—` | Operator: delete channel metadata from project data_channels (ecosystem owner). |
| `social.admin.force_delete_message` | `—` | Operator: delete a chat message by id from DNA ring (ecosystem owner). Use dry_run first. |
| `social.admin.graph_edges` | `—` | Operator: list friend and blocked edges for a user (capped). |
| `social.admin.messenger_client_cache_get` | `—` | Operator: read messenger client cache limits (defaults, stored overrides, merged effective). |
| `social.admin.messenger_client_cache_set` | `—` | Operator: replace data.ecosystem.messenger_client_cache on ecosystem project (media/IDB/OPFS caps). |
| `social.admin.messenger_policy_get` | `—` | Operator: read messenger retention defaults, stored overrides, and merged effective policy. |
| `social.admin.messenger_policy_set` | `—` | Operator: replace messenger_operator_policy on ecosystem project (tiers, temp TTL, sweep ceiling). |
| `social.admin.public_index_status` | `—` | Operator: public index page or reconcile maps (set reconcile=true). |
| `social.admin.relay_hints` | `—` | Operator: static hints for stream relay room naming and diagnostics paths. |
| `social.admin.reset_messenger_prefs` | `—` | Operator: clear messenger_prefs for a user (reason required). |
| `social.admin.revoke_federation` | `—` | Operator: remove a federation pointer from consumer to source channel. |
| `social.admin.user_summary` | `—` | Operator: aggregate friend/request/block counts and messenger prefs summary for a user_id. |
| `social.channel_invites.accept` | `—` | Accept a channel invite from inbox. |
| `social.channel_invites.decline` | `—` | Decline a channel invite. |
| `social.channel_invites.direct` | `—` | Create direct channel invite to a user (owner). |
| `social.channel_invites.inbox` | `—` | List pending channel invites for the current user. |
| `social.channel_invites.link_token` | `—` | Create shareable link token for a channel (owner). |
| `social.channel_invites.redeem` | `—` | Redeem channel invite token. |
| `social.channels.delete` | `—` | Delete a channel (owner) and unpublish from public index. |
| `social.channels.get` | `—` | Get one channel if the current user may access it. |
| `social.channels.list` | `—` | List all channels in the home project. |
| `social.channels.register` | `—` | Create a new messenger channel (group chat, DM, or broadcast). |
| `social.channels.update` | `—` | Update channel metadata (owner). |
| `social.chat.crdt_state` | `—` | Read the current CRDT snapshot + raw updates for a channel. |
| `social.chat.crdt_update` | `—` | Apply a Y.js CRDT update to a channel's shared document (bodies / pins / deletes). |
| `social.chat.delta` | `—` | Unified incremental delta for a messenger channel — one call returns new messages, |
| `social.chat.history` | `—` | Read chat history from a messenger channel (AI-accessible). |
| `social.chat.index_get` | `—` | Get the current user's messenger sidebar index — list of channels with pinned/order metadata. |
| `social.chat.index_put` | `—` | Merge chat index entries into the user's sidebar index (no If-Match; use social flows for removal). |
| `social.chat.message_delete` | `—` | Delete a chat message (author or channel owner per server rules). |
| `social.chat.message_edit` | `—` | Edit own chat message (author only). |
| `social.chat.pin` | `—` | Pin a message in a channel. |
| `social.chat.post` | `—` | Send a chat message to a messenger channel as the AI agent (authenticated user). |
| `social.chat.presence_online` | `—` | List user IDs currently online (active SSE stream-relay connection) in the home project. |
| `social.chat.reaction` | `—` | Apply one server-authoritative reaction on a message (immutable cell per user). |
| `social.chat.read_get` | `—` | Get last-read state for a channel for the current user. |
| `social.chat.read_set` | `—` | Update last-read pointer (read receipt) for a channel. |
| `social.chat.unpin` | `—` | Unpin a message in a channel. |
| `social.entities.expand` | `—` | Batch-resolve channel/surrogate ids to metadata (max 100 ids). |
| `social.federation.add` | `—` | Add federated open-channel pointer on consumer project. |
| `social.federation.get` | `—` | List federation pointers for a consumer project. |
| `social.followers.list` | `—` | Users who follow the current user. |
| `social.friends.accept` | `—` | Accept friend request from user_id. |
| `social.friends.block` | `—` | Block a user. |
| `social.friends.cancel` | `—` | Cancel outgoing friend request. |
| `social.friends.cards` | `—` | Public card fields for each friend. |
| `social.friends.cards_batch` | `—` | Friend cards for specific user ids (must be friends). |
| `social.friends.list` | `—` | List friends for the current user. |
| `social.friends.reject` | `—` | Reject a pending friend request. |
| `social.friends.remove` | `—` | Remove an existing friend. |
| `social.friends.request` | `—` | Send a friend request (optional source context for public_channel). |
| `social.friends.request_respond` | `—` | Accept/reject/subscriber/block on an incoming friend request. |
| `social.friends.requests_in` | `—` | Incoming friend requests. |
| `social.friends.requests_out` | `—` | Outgoing friend requests. |
| `social.friends.user_ids` | `—` | Ordered friend user id list. |
| `social.merge_feed` | `—` | Merge read of multiple channel pointers (reader must access each). |
| `social.party_invite.mint` | `—` | Mint a short-lived party invite token for a relay room. |
| `social.party_invite.verify` | `—` | Verify party invite token (no auth required on REST; MCP still needs project context). |
| `social.pas.public_me_get` | `—` | Read Principal Access public slice for current user on a home project. |
| `social.pas.public_me_put` | `—` | Replace channels/chats/groups lists in PAS public slice. |
| `social.privacy.get` | `—` | Get friend/DM privacy settings. |
| `social.privacy.put` | `—` | Update privacy settings (incoming_friend_requests, dm_policy). |
| `social.public.index` | `—` | Read a page of the public discovery index. |
| `social.public.me` | `—` | Public maps from user row (ecosystem.social.public). |
| `social.public.publish` | `—` | Publish a channel/chats/groups entry to public index. |
| `social.public.quota_get` | `—` | Social public index quota for current user. |
| `social.public.reconcile` | `—` | Reconcile public maps (privileged role only). |
| `social.public.unpublish` | `—` | Remove a public index entry. |
| `social.support.eligibility` | `project_support` | Batch read: for home project ids the user can access, returns whether end-user private support and/or public Messenger support lounge are enabled (stable **project-scoped** channel id when the lounge is public). |
| `social.support.history` | `—` | Read project support thread for the authenticated user (stable **per-user** support thread channel id). |
| `social.support.inbox` | `project_support` | Staff inbox preview list for a project (requires support access). |
| `social.support.send` | `—` | Send a message in the user's support thread for a project (user) or staff reply when permitted. |
| `social.users.lookup` | `—` | Find users by id, email, or @username (no email in response). |
| `social.users.public_cards` | `—` | Public display cards for user ids (no friendship check). |

## storage (4)

| Action | Required cap | Summary |
|--------|--------------|---------|
| `storage.delete_file` | `—` | Deletes a file from ecosystem storage (JSON registry + blob). Requires write access for user scope; owner/admin for project scope. |
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
