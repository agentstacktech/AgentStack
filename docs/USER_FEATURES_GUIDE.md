# Using AgentStack — Platform features (for account holders)

This guide is for **people using AgentStack through the website** ([agentstack.tech](https://agentstack.tech)): sign in, pick a project, and work in the **dual-shell SPA** (`/user/*` for operators, `/dev/*` for builders). It summarizes **recent capabilities** (RAG, CRM, Storefront Studio, sandboxes, access control) in plain language and points to deeper docs when you need API or automation details.

**Language:** This public documentation is **English only**.  
**Canonical routes:** [dual-shell/README.md](dual-shell/README.md) (user vs dev hub) · Living Plane: `docs.freshness.living.gen1`

---

## 1. Dual-shell workspace (web)

After you log in, open **User** or **Dev** shell and select a **project**. Surfaces you may see include:

| Surface | What you use it for |
|---------|---------------------|
| **Overview / Discover** | Project snapshot; capability map at `/user/discover` or `/dev/discover` |
| **Project settings** | Name, type, and project-level options |
| **CRM** | Contacts, companies, deals on the project (`/user|dev/projects/:id/crm`) |
| **Assets / Storefront Studio** | Catalog and studio seed (`?mode=studio` on assets) |
| **Integrations / iPaaS** | App Directory, OAuth connections, draft/live scenarios, DataMapper, runs |
| **Agents / Bots** | Fleet AI and bot studio |
| **Support** | Project support threads (staff/user) |
| **RAG** | Knowledge collections, ingest, semantic search, session memory |
| **Storage / Sites** | Files and hosted sites publish (`/s/...`) |
| **Logic** | Rules, triggers, flow editor |
| **RBAC / Field access** | Roles and response field masking |
| **Docs cookbook** | How-to hubs at `/dev/docs/{build,scale,secure,operate,extend}` |

Exact labels depend on your **subscription** and permissions.

**Engineering passport (full shipped catalog for AI/investors):** [WHATS_NEW.md](WHATS_NEW.md) · [monorepo FEATURES_SHIPPED](https://github.com/agentstacktech/agentstack/blob/main/docs/project-profile/FEATURES_SHIPPED.md)

**URL patterns (examples):**

- `https://agentstack.tech/dev/projects/<projectId>/crm` — CRM workspace  
- `https://agentstack.tech/dev/discover` — capability discovery  
- Legacy `/dashboard/<projectId>?module=…` may still redirect; **prefer dual-shell paths** above.

---

## 2. RAG — knowledge and memory for your project

**RAG** (Retrieval-Augmented Generation) lets you attach **searchable knowledge** to a project: upload text, search by meaning, and (where enabled) keep a **short memory** of a conversation session so AI tools can stay on topic.

**Typical workflow in the UI**

1. Open the project in **Dev/User shell** and go to the **RAG** surface (or Discover → RAG).
2. Create a **collection** (a bucket for related documents).
3. **Add documents** (text is chunked and indexed automatically).
4. Run **semantic search** inside a collection.
5. Optionally use **session memory** features if your workflow needs recent turns indexed for retrieval.

**Limits** depend on your **subscription** (collections, chunks, memory rows, etc.). If an action is blocked, check your plan or upgrade path on the site.

*More detail (includes REST `/api/rag/` and MCP for automations):**  
[RAG_PLATFORM_GUIDE.md](RAG_PLATFORM_GUIDE.md)

---

## 3. Sandboxes and Playground (isolated environments)

**Sandboxes** let you work in an **isolated environment** derived from your project: try changes, checkpoints, and (on supported plans) promotion flows back toward production — without replacing your live data blindly.

This is aimed at teams that need **safe experimentation** and controlled rollout. Day-to-day users may only see sandbox features if the product surface exposes them for your account.

**Deeper technical guides (APIs, limits, flows):**

- [SANDBOX_PLAYGROUND_GUIDE.md](SANDBOX_PLAYGROUND_GUIDE.md) — concepts, subscription gates, and patterns.  
- [SANDBOX_AND_ENVIRONMENTS.md](SANDBOX_AND_ENVIRONMENTS.md) — REST API and environment behaviour.

**API reference:** [Swagger UI](https://agentstack.tech/swagger) — look for **Sandbox**-related tags and [OPENAPI.md](OPENAPI.md).

---

## 4. Who sees what data (access control)

AgentStack uses **layered access**:

1. **API keys** — may be restricted to certain services (automation safety).
2. **Roles (RBAC)** — who may open which parts of the product and call which APIs.
3. **Field Access Policy (FAP)** — which **fields** appear in JSON for each role (read, mask, or hide).

As a project owner or admin, you may configure **field access** in the dashboard. As a member, you simply see what your role allows.

**Readable overview:** [ACCESS_AND_FIELD_POLICY.md](ACCESS_AND_FIELD_POLICY.md)  
**Full policy format:** [FIELD_ACCESS_POLICY.md](FIELD_ACCESS_POLICY.md)

---

## 5b. CRM and Storefront (Sell & Operate)

**CRM** keeps **contacts, companies, and deals** on the active project. Open `/user|dev/projects/<id>/crm`. Automations can create contacts from webhooks or from **checkout → CRM** when an order completes.

**Storefront Studio** fills a product catalog / vitrine with low input (`?mode=studio` on assets): plan → apply → undo. Pair with **Sites** hosting for a public `/s/...` storefront.

Deeper: [crm/README.md](crm/README.md) · [how-to/crm/INTEGRATION_QUICKSTART.md](how-to/crm/INTEGRATION_QUICKSTART.md)

---

## 5c. Integrations (Connect & Automate)

The **Integration Hub** (iPaaS) lets you:

1. Browse the **App Directory** and install a starter  
2. Connect via **OAuth** (or API credentials where applicable)  
3. Author a **scenario** (draft → live) on the Logic canvas  
4. Map fields with **DataMapper**, run a **dry test-step**, then inspect **runs**

Natural-language “copilot” suggestions may appear as a **preview** — treat them as assistive, not a guaranteed LLM product.

Deeper: [how-to/crm/INTEGRATION_QUICKSTART.md](how-to/crm/INTEGRATION_QUICKSTART.md) · [support/INTEGRATION_QUICKSTART.md](support/INTEGRATION_QUICKSTART.md) (integrations hub)

---

## 5d. Project hosting (Sites)

Publish static files or a ZIP to a live URL under `/s/{project}/{bucket}/`. Use **Storage → Sites**, the **host-site** public funnel, or MCP `hosting.*` from an IDE agent. The project **hosting plane** suggests next steps on the Host → Sell → Scale ladder.

Custom domains and guest publish-without-login are **not** general-availability marketing promises — see the capabilities matrix.

Deeper: [hosting/HOSTING_GOLDEN_PATHS.md](hosting/HOSTING_GOLDEN_PATHS.md)

---

## 5e. Business organism (command center)

**Business organism** lets you model a project as a **head** with attachable **organs** (child projects, treasury, sharing rules). Open `/user|dev/projects/<id>/business` or follow the in-app cookbook recipe **Business organism setup** (`/dev/docs/operate/business-organism-setup`).

Typical workflow:

1. Create or select a **business head** project.
2. Use **Create composite** to attach organs (CRM, bots, hosting, etc.).
3. Review treasury and sharing from the command center.

Honesty: federation and list-for-sale flows are **builder surfaces** — see cookbook recipe **Business organism setup** (`/dev/docs/operate/business-organism-setup`).

---

## 5f. Knowledge platform (policy + mentor)

**Knowledge ops** applies **policy templates** (grounding, abstain rules) and smoke-tests the knowledge playground. Surfaces are primarily in the **Dev shell** (`/dev/projects/<id>/knowledge-ops`). Operators in User shell can open **Discover → Guides** for the same recipe highlight.

GetCourse import and crisis editor tooling are **operator-only** — not marketed as self-serve for all tenants.

Cookbook: `/dev/docs/operate/knowledge-policy-launch` · Compass playbook `launch-knowledge-assistant`.

---

## 5g. LLM energy and BYOK

Project **LLM energy** covers prepaid compute credits and optional **bring-your-own-key (BYOK)** routing for agent runs. Configure under **Project settings → LLM** or the Secure cookbook recipe **LLM energy & BYOK** (`/dev/docs/secure/llm-energy-byok`).

Usage limits and pricing tiers are on [agentstack.tech/pricing](https://agentstack.tech/pricing) — counts come from the live capability matrix, not hardcoded docs.

---

## 5. Subscriptions and limits

Plans differ by projects, members, API usage, storage, RAG, sandbox features, support, and more.

**Comparison table:** [subscription/SUBSCRIPTION_TIERS.md](subscription/SUBSCRIPTION_TIERS.md)  
**Anonymous trial-style usage:** [subscription/ANONYMOUS_TIER.md](subscription/ANONYMOUS_TIER.md)

**Pricing and billing UI:** use [agentstack.tech](https://agentstack.tech) (Pricing / Billing). For raw API details, use [Swagger](https://agentstack.tech/swagger).

---

## 6. AI Builder

The **AI Builder** may be presented as a **demo** of the ecosystem or temporarily unavailable on the public site. When it is off, use the rest of the dashboard, APIs, and MCP integrations for your workflows.

---

## 7. Connecting external AI (Cursor, Claude, GPT, VS Code)

If you use **MCP** or plugins, start from the ecosystem index and the plugins list:

- [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md)  
- [plugins/README.md](plugins/README.md)

---

## 8. Ecosystem — multiple projects on one platform

When you run **more than one project** (or work with **partners** who have their own AgentStack projects), **ecosystem channels** let you share **named events** and **data-field updates** across project boundaries—with **access rules** and a **public field list** so sensitive data stays internal.

**In the product:** open **Project settings → Ecosystem** to publish channels and manage grants. The marketing/docs site may also expose **Ecosystem** documentation at `/ecosystem-docs`.

**Architecture and patterns (star, hub, B2B, multi-tenant, safety checklist):**  
[ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md)

**REST details and examples:**  
[ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md)

---

## Quick links


| Topic                              | Document                                                                       |
| ---------------------------------- | ------------------------------------------------------------------------------ |
| Full doc index                     | [README.md](README.md)                                                         |
| Shipped features (detailed)        | [WHATS_NEW.md](WHATS_NEW.md) · [FEATURES_SHIPPED (monorepo)](https://github.com/agentstacktech/agentstack/blob/main/docs/project-profile/FEATURES_SHIPPED.md) |
| Ecosystem networks (cross-project) | [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) |
| MCP + ecosystem index              | [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md)                                   |
| OpenAPI / Swagger                  | [OPENAPI.md](OPENAPI.md)                                                       |
| REST API topics                    | [api/README.md](api/README.md)                                                 |
