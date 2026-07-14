# Using AgentStack — Platform features (for account holders)

This guide is for **people using AgentStack through the website** ([agentstack.tech](https://agentstack.tech)): sign in, pick a project, and work in the **dashboard**. It summarizes **recent capabilities** (RAG, sandboxes, access control) in plain language and points to deeper docs when you need API or automation details.

**Language:** This public documentation is **English only**.

---

## 1. Dashboard (web)

After you log in, open **Dashboard** and select a **project**. Modules you may see include:


| Module                                 | What you use it for                                                                                                                        |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Overview**                           | Project snapshot and entry to other areas.                                                                                                 |
| **Project settings**                   | Name, type, and project-level options.                                                                                                     |
| **Sessions**                           | Active sessions tied to your project.                                                                                                      |
| **RBAC**                               | Roles and permissions for people on the project.                                                                                           |
| **Field access**                       | Who can see which fields in API responses (masking / hiding sensitive data).                                                               |
| **RAG**                                | Knowledge collections, document ingest, semantic search, and optional **session memory** for AI context.                                   |
| **Buffs**                              | Trials, subscriptions, and promotional entitlements when enabled for your project.                                                         |
| **Ecosystem** (under project settings) | Publish **channels** so other AgentStack projects can subscribe to your events or field updates—with controlled access and visible fields. |


Exact labels depend on your **subscription** and permissions. If a module is missing, your role or plan may not include it.

**URL pattern (examples):**  
`https://agentstack.tech/dashboard/<projectId>?module=rag` — opens the dashboard with the **RAG** module selected (when available).

---

## 2. RAG — knowledge and memory for your project

**RAG** (Retrieval-Augmented Generation) lets you attach **searchable knowledge** to a project: upload text, search by meaning, and (where enabled) keep a **short memory** of a conversation session so AI tools can stay on topic.

**Typical workflow in the UI**

1. Open **Dashboard → RAG** for your project.
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
| Ecosystem networks (cross-project) | [ECOSYSTEM_API_IMPLEMENTATION.md](ECOSYSTEM_API_IMPLEMENTATION.md) |
| MCP + ecosystem index              | [MCP_AND_ECOSYSTEM.md](MCP_AND_ECOSYSTEM.md)                                   |
| OpenAPI / Swagger                  | [OPENAPI.md](OPENAPI.md)                                                       |
| REST API topics                    | [api/README.md](api/README.md)                                                 |
