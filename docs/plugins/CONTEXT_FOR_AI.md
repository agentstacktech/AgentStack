# AgentStack — Capability Map for AI

**Purpose:** Single reference for AI (Custom GPT, VS Code, integrations): which domain to use and which tool groups to call for a user request. Use this document to decide "when user asks X → use tools from domain Y." Full tool list and parameters: [MCP_SERVER_CAPABILITIES.md](../MCP_SERVER_CAPABILITIES.md) in the repo.

**Data store:** AgentStack has a **JSON-based data store** (the “database”): each project and each user has a JSON document (`project.data`, `user.data`). Read/write via **key-value API** (GET/POST `/data` with keys `project.data.<path>`, `user.data.<path>`) or via **project API** (`projects.get_project` returns project including `data`; `projects.update_project` accepts `data`). See [DNA_KEY_VALUE_API.md](../architecture/DNA_KEY_VALUE_API.md).

---

## Domain map

| Domain | When to use (triggers) | Key tool groups | Example: user request → tool |
|--------|------------------------|-----------------|-----------------------------|
| **8DNA (JSON+)** | Data store, database, structured data, data/config design; “where is data stored?”; A/B tests, variants, experiments | (Data model; project/data APIs, key-value API) | JSON+ = structured JSON with built-in support for structure and variants (e.g. A/B tests). Store/read via key-value API or `projects.get_project`/`update_project`. "Where is data stored?" → project.data, user.data; key-value API or project API. "A/B tests?" → JSON+ supports variants; see repo docs. |
| **Projects** | Create project, list projects, API key, stats, settings, activity, attach anonymous project | projects.* | "Create a project" → `projects.create_project_anonymous` with name. "List my projects" → `projects.get_projects`. "Stats for project X" → `projects.get_stats` with project_id. |
| **Rules Engine** | When/then, triggers, automation, event-driven logic, no-code rules | logic.*, rules.* | "When user signs up, give trial" → create rule via `logic.create` or `rules.create_rules`; action e.g. buffs.apply_temporary_effect. "List rules" → `logic.list`. |
| **Assets** | Inventory, trading, in-game items, digital goods, catalog | assets.* | "Create an asset" → `assets.create`. "List assets for project" → `assets.list` with project_id. |
| **RBAC** | Roles, permissions, assign role, list users by role, project membership | auth.assign_role, projects.get_users, projects.update_user_role, projects.add_user, projects.remove_user | "Assign admin to user" → `projects.update_user_role` (project_id, user_id, role). "List admins" → `projects.get_users` with role filter. |
| **Buffs** | Trial, subscription, promo, apply effect, limits, temporary/persistent effects | buffs.* | "Give user 7-day trial" → `buffs.apply_temporary_effect`. "List active subscriptions" → `buffs.list_active_buffs`. "Effective limits" → `buffs.get_effective_limits`. |
| **Payments** | Payment, refund, balance, transactions, wallet, stripe, tochka | payments.*, wallets.* | "Create payment" → `payments.create_payment`. "Payment status" → `payments.get_status`. "Refund" → `payments.refund`. "Wallet balance" → `wallets.get_balance`. |
| **Auth** | Login, sign in, register, profile, session, identity (for roles use RBAC) | auth.* | "Log in" → `auth.quick_auth` (email, password). "Register" → `auth.create_user`. "Get profile" → `auth.get_profile`. "Update profile" → `auth.update_profile`. |
| **Other** | Scheduling, analytics, webhooks, notifications | scheduler.*, analytics.*, webhooks.*, notifications.* | See MCP_SERVER_CAPABILITIES for full list and parameters. |

---

## Full tool list

**Full list of MCP tools and parameters:** [MCP_SERVER_CAPABILITIES.md](../MCP_SERVER_CAPABILITIES.md) in the AgentStack repo. Use it for exact tool names, request/response shapes, and when a domain has more tools than listed above.

---

**Version:** 0.1  
**Source:** [AGENTSTACK_PLUGIN_PHILOSOPHY.md](AGENTSTACK_PLUGIN_PHILOSOPHY.md), plugin skills (Cursor/Claude). Update this map when MCP capabilities change.
