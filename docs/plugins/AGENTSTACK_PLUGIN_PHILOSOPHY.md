# AgentStack Cursor Plugin — Philosophy validation

**Version:** 0.1  
**Date:** 2026-02-22  
**Status:** Reference for plugin development  
**Source:** Platform philosophy index (monorepo — not mirrored here).

---

## Document purpose

Describes how each component of the AgentStack plugin for Cursor Marketplace aligns with PHILOSOPHY_INDEX. Every development step and every plugin artifact is checked through this lens.

---

## Principles and application to the plugin

### 1. Creation over Conflict (v0.2.56)

**Principle:** Don't fight the problem — create a world where it doesn't exist.

**Application to the plugin:**

- **Don't compete with Supabase.** Don't position the plugin as "beating Supabase". Create an offer where choosing AgentStack is natural: full ecosystem (8DNA, Rules Engine, Buffs, single MCP tool `agentstack.execute` with live actions) vs "just DB + Auth".
- **README and description:** Focus on capabilities and scenarios, not on comparison-against. Comparison table is informative (what AgentStack has), not attacking.
- **Skills and Rules:** Provide a "world without boilerplate" — Rules Engine instead of manual triggers, Buffs instead of custom trial/subscription logic.

**Validation:** When writing plugin copy ask: "Are we creating value or fighting someone?" — only the first.

---

### 2. Time-Decomposition-Completion (v0.2.54)

**Principle:** Complex tasks are solved step by step; always finish what you start according to the plan.

**Application to the plugin:**

- **Plugin is split into phases (0–6).** Each phase ends with explicit artifacts (document, file, checklist).
- **Quick Start MCP:** User should get a working MCP in 2–3 steps (get API key → add to config → done). Each step is complete and verifiable.
- **Skills:** Each skill is a complete module (goal, when to use, examples, references).

**Validation:** No "half-finished" instructions. Every plan item has a clear outcome.

---

### 3. Decomposition & Modular Design (v0.2.53)

**Principle:** "Eat the elephant one bite at a time." A complex system is a set of manageable modules.

**Application to the plugin:**

- **Plugin = set of independent components:** Rules (.mdc), Skills (SKILL.md), MCP (config). Can enable/disable separately.
- **Three Skills:** 8DNA, Projects & MCP, Rules Engine — different domains, reusable separately.
- **Two Rules:** DNA patterns and JSON/HTTP API (/api/*) — knowledge modules without duplication.

**Validation:** Changing one component does not break others. No monolithic "everything in one" file.

---

### 4. Elegant Minimalism

**Principle:** The ideal system fulfills its function with minimal requirements and complexity.

**Application to the plugin:**

- **Minimal configuration:** Ideally "install and it works" (cloud MCP + one API key). Self-hosted — option, not requirement.
- **plugin.json:** Concise description, precise keywords. No marketing noise.
- **Documentation:** Short Quick Start; details via links (MCP_CAPABILITY_MATRIX, ECOSYSTEM_API_IMPLEMENTATION).
- **Security review:** Transparency (what data, where requests go) as the basis for trust — no extra promises.

**Validation:** Every added setup step is justified. Prefer linking to existing docs over copying walls of text.

---

### 5. 8DNA Biological Computing (v0.2.1)

**Principle:** Data as an evolutionary system: hierarchy (parent_uuid), generations (generation), genetic key encoding.

**Explanation for users and AI (public):** We describe 8DNA as **JSON+** — structured JSON with built-in support for variants (e.g. A/B tests). Data store: project.data and user.data; access via key-value API (GET/POST /data) or project API (get_project, update_project). See **docs/architecture/DNA_KEY_VALUE_API.md**. Architecture details (hierarchy, evolution) — in internal docs and repo philosophy, do not expose before patents.

**Application to the plugin:**

- **Skill 8DNA:** Translates to AI skill: 8DNA = JSON+ (structured JSON, built-in variants, e.g. A/B); explicitly mention data store and DNA_KEY_VALUE_API. Do not expose parent_uuid, generation, lineage publicly; details in internal docs/repo philosophy.
- **Rule DNA-patterns:** Consistent recommendations for data, config, protected structure and genetic coding — so projects don't invent their own worlds.
- **Positioning:** 8DNA vs flat tables — not "better/worse", but "a different world" (hierarchy and evolution out of the box).

**Validation:** Skills and Rules do not distort 8DNA architecture; they point to official philosophy and docs.

---

### 6. Time Processes Philosophy (v0.2.52)

**Principle:** Time is the foundation of processes.

**Application to the plugin:**

- **CHANGELOG and versioning:** Every plugin release has an explicit version and date. Plugin evolution is traceable.
- **Post-release:** Iterations on Skills/Rules from feedback — a timeline of improvements.

**Validation:** Versions and change history are kept consistently.

---

## Plugin components summary (gen1 — archived)

> **Current:** Cursor plugin **gen3** (v0.4.14) — see section below. Gen1 skill names (`agentstack-8dna`, `agentstack-rules-engine`, …) are retired.

| Component | Creation over Conflict | Decomposition | Elegant Minimalism | 8DNA / Time |
| --------- | ---------------------- | ------------- | ------------------ | ----------- |
| **Five layers + catalog** | Prefer MCP over custom backend | One skill per domain cluster | Live catalog only (`GET /mcp/actions`) | OAuth Device Code + CHANGELOG |
| **Rules (T0/T1)** | Standing orders + globs | `agentstack-prefer` + domain rules | ≤2 alwaysApply | — |
| **Skills (18+)** | Decision-first routers | hosting, support, storage, messenger, … | Link to matrix, no action tables | — |
| **Commands / Agents** | Verifiable install & ops flows | Side effects only in commands | `/agentstack-init` canonical | — |
| **Hooks** | Secret scan, token refresh | Out-of-band only | Contract tests in CI | — |
| **Publication gate** | No doc drift | `audit-cursor-plugin.mjs` | CANONICAL_COPY.md | — |

---

## Skills: validation against PHILOSOPHY_INDEX

When changing any skill or skill instructions, verify against the checklist below against platform philosophy principles (Creation over Conflict, Decomposition, Elegant Minimalism).

| Criterion | Check question |
| --------- | --------------- |
| **Creation over Conflict** | Are we creating value (world without boilerplate), not fighting alternatives? |
| **Time-Decomposition-Completion** | Is the skill a complete module (goal, when to use, examples, references)? No half-finished instructions? |
| **Decomposition** | One skill — one domain? Changing one does not break others? |
| **Elegant Minimalism** | Minimum text, links instead of copy-paste, SKILL.md up to ~500 lines? |
| **8DNA / Time** | Skill 8DNA matches official architecture; versions and CHANGELOG when evolving skills? |

Every subsequent development or skill edit is explicitly checked against this checklist.

---

## Summary

The AgentStack plugin for Cursor is not "another plugin against Supabase" but **creating an offer**: a full backend ecosystem with 8DNA, Rules Engine, Buffs, and one MCP tool (`agentstack.execute`) with live actions. Each component is modular, minimally sufficient, and aligned with PHILOSOPHY_INDEX.

---

## Claude Plugin — same lens

The AgentStack plugin for **Claude Code** ([claude-plugin](https://github.com/agentstacktech/claude-plugin)) is validated by the same principles. Differences: manifest `.claude-plugin/plugin.json`, MCP is configured by the user via `claude mcp add --transport http` (see [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md)); there is no Cursor Rules (.mdc) equivalent — knowledge is in Skills and doc links.

### Component summary (Claude)

| Component | Creation over Conflict | Decomposition | Elegant Minimalism | 8DNA / Time |
| --------- | ---------------------- | ------------- | ------------------ | ----------- |
| **plugin.json** | Ecosystem value description | Manifest module | Conciseness, precise keywords | version in manifest |
| **MCP_QUICKSTART** | "Install and it works" (one API key + one command) | Quick Start as separate flow | Minimum steps, links to docs | — |
| **Skill 8DNA** | World of hierarchy and evolution | One skill — one domain | Links to docs | Full 8DNA alignment |
| **Skill Projects** | World without manual project setup | Separate skill from Rules Engine | "One request — result" examples | — |
| **Skill Rules Engine** | World without boilerplate triggers | Separate skill | When to use Rules vs code | — |
| **Skill Assets** | World of trading/games/inventory without custom tables | Separate skill (assets.*) | Links to MCP, link to wallets/buffs | — |
| **Skill RBAC** | Roles and permissions without custom access logic | Separate skill (auth.*, projects.*) | Links to MCP and RBAC API/SDK | — |
| **Skill Buffs** | Trials/subscriptions without custom logic | Separate skill (buffs.*) | Links to MCP, link to Rules Engine | — |
| **Skill Payments** | Payments and wallets via ecosystem | Separate skill (payments.*, wallets.*) | Links to MCP and PAYMENT_GATEWAY | — |
| **Skill Auth** | Login/profile without custom tables | Separate skill (auth.*), roles in RBAC | Links to MCP | — |
| **README** | Door to "AgentStack world" | Sections: what it is, Quick Start, comparison | Capabilities table, not walls of text | CHANGELOG/versions |
| **TESTING_AND_CAPABILITIES** | — | Clear verification steps | Links to MCP_QUICKSTART and docs | — |

---

## GPT (OpenAI) Plugin — same lens

The AgentStack plugin for **ChatGPT** ([gpt-plugin](https://github.com/agentstacktech/gpt-plugin)) — artifacts for GPT Actions: OpenAPI 3.1 schema + Custom GPT instructions. Installation = creating a Custom GPT per GPT quick start; MCP is shared (see [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md)).

### Component summary (GPT)

| Component | Creation over Conflict | Decomposition | Elegant Minimalism | 8DNA / Time |
| --------- | ---------------------- | ------------- | ------------------ | ----------- |
| **OpenAPI schema** | Ecosystem value description, one action | Schema module | Single execute_tool operation, links to MCP_CAPABILITY_MATRIX | version in info |
| **GPT_INSTRUCTIONS** | Context and scenarios (projects, stats, keys) | Separate reference | Minimum steps, do not log keys | Mention 8DNA in Context |
| **GPT_QUICKSTART** | "Created Custom GPT and it works" | Quick Start as separate flow | 5–7 steps, links to docs | — |
| **README** | Door to "AgentStack world" for ChatGPT | Sections: what it is, Quick Start | Artifacts table, links | CHANGELOG/versions |
| **ARTIFACTS.md** | Unified artifacts format | Layout description | Where things live, schema regeneration | — |
| **TESTING_AND_CAPABILITIES** | — | Manual verification checklist | Links to MCP_CAPABILITY_MATRIX | — |
| **OpenAPI generator** | Single source of truth (MCP_TOOLS_REGISTRY) | Platform MCP registry | Documented in ARTIFACTS | — |

---

## VS Code Plugin — same lens

The AgentStack plugin for **VS Code** ([vscode-plugin](https://github.com/agentstacktech/vscode-plugin)) — gen1 (`repo.plugins.vscode.gen1`): **Marketplace build** uses stable APIs — `@agentstack` chat participant, sidebar tree, Device Code + SecretStorage, optional LM tools (`agentstack_execute`). **Manual MCP** via the repo MCP quick start. Proposed `McpServerDefinitionProvider` is dev/enterprise only when API stabilizes. See [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md).

### Component summary (VS Code)

| Component | Creation over Conflict | Decomposition | Elegant Minimalism | 8DNA / Time |
| --------- | ---------------------- | ------------- | ------------------ | ----------- |
| **package.json** | Live catalog copy (no fixed action count) | Manifest, chatSkills, LM tools | Lazy activation | version in manifest |
| **extension.ts** | One production path per surface | Thin shell → `app/registerExtension.ts` | No dual Marketplace+proposed runtime | — |
| **Ecosystem Tree View** | Ecosystem map at hand, no digging through repo | One node = one domain; block "Same MCP: Cursor, Claude, GPT" | Minimal text in nodes, details via link | 8DNA as domain with link to DNA_KEY_VALUE_API |
| **Status bar** | Connection status without fighting configuration | One click — Set API Key or Show API key & project info | Minimum actions | — |
| **Open documentation** | Single entry point to docs | One command → plugins index | One item in Command Palette | — |
| **SecretStorage** | "Install and it works" after first key entry | Separate resolve vs provide logic | Do not ask for key again | — |
| **Skill Rules, Assets, RBAC, Payments, Auth** | World without boilerplate (Rules, Buffs, Payments, Auth) | One skill — one domain | Links to MCP_CAPABILITY_MATRIX, CONTEXT_FOR_AI | — |
| **README / MCP_QUICKSTART** | Door to "AgentStack world" for VS Code | Sections: what it is, Quick Start, Ecosystem view | Links to MCP_CAPABILITY_MATRIX | CHANGELOG/versions |
| **TESTING_AND_CAPABILITIES** | — | Clear verification steps (F5, Set API Key, chat) | Links to MCP_QUICKSTART and docs | — |

**Genetic code (reference):** `docs.plugins.agentstack_plugin_philosophy.gen2`

---

## Cursor Plugin v0.4.14 — gen3 (catalog plane)

**Genes:** `repo.plugins.cursor.gen3`, `repo.plugins.publication_gates.gen1`, `repo.plugins.hooks.contract.gen1`, `repo.plugins.oauth_device_code.gen1`, `repo.plugins.capability_routing.gen1`.

Five layers unchanged; **catalog plane** = live `GET /mcp/actions` + `gen_capability_matrix.py --check` + local snapshot. Skills split (hosting / support / storage vs legacy `support-storage`). CI: plugin audit script in the cursor-plugin repo.

### Validation (gen3)

- `node provided_plugins/scripts/audit-cursor-plugin.mjs`
- `node provided_plugins/cursor-plugin/scripts/test-hooks-contract.mjs`
- `provided_plugins/cursor-plugin/scripts/validate-plugin.mjs --strict-screenshots` (release)

---

## Cursor Plugin v0.4.9 — the 5-layer model (gen2, historical)

**Genes:** `repo.plugins.cursor.gen2`, `repo.plugins.oauth_device_code.gen1`, `repo.plugins.capability_routing.gen1`.

The v0.4.9 Cursor plugin is a **clean break** from gen1. Content now lives in five orthogonal layers; each layer serves a different moment in the AI/user loop.

| Layer | Artifacts | When it fires | Philosophy anchor |
|-------|-----------|---------------|--------------------|
| **1. Rules** | `rules/*.mdc` with `alwaysApply: true` | Every agent turn | Creation over Conflict (prefer MCP over new backend) |
| **2. Skills** | `skills/<domain>/SKILL.md` | Triggered by intent keywords in user text | Decomposition (one skill — one domain) |
| **3. Commands** | `commands/*.md`, invoked as `/agentstack-*` | User types a slash command | Time-Decomposition-Completion (each command ends with verifiable artifact) |
| **4. Agents** | `agents/*.md`, selected via `@agentstack-*` | Long-running multi-domain tasks | Evolution of Organs (architect + migrator as dedicated specialists) |
| **5. Hooks** | `hooks/hooks.json` + `hooks/scripts/*.mjs` | Cursor lifecycle events (sessionStart, beforeShellExecution, postToolUse, afterFileEdit) | Biological Computing (nervous-system reflexes) |

### Why five layers

- Rules alone miss follow-up intent clusters; skills alone leak into unrelated prompts. Together they give **decision-first routing** with fallback guard rails.
- Commands give users an **explicit**, auditable entry into flows that involve side effects (OAuth, scaffolding, migration).
- Agents are the only layer that can sustain a **multi-domain** plan across many turns without drifting.
- Hooks are the only layer that runs **without a prompt** — they enforce security, telemetry, and token freshness out of band.

### One-click install — Creation over Conflict in action

The Device Code flow (`hooks/scripts/device-code.mjs` + `/api/oauth2/device/approve` + activate page) removes the manual API-key copy-paste step.

### Capability routing — one source of truth

`GET /mcp/actions` is the only list of capabilities the agent should read. `docs/MCP_CAPABILITY_MATRIX.md` is the stable public link; `docs/plugins/CAPABILITY_MATRIX.md` remains the generated fallback while the generator output lives there, so the skills never copy-paste action lists.

### Validation

- `node provided_plugins/cursor-plugin/scripts/validate-plugin.mjs` — structural lint (hooks.json script references, frontmatter presence, trigger-keyword count ≥ 3, streamable-http, manifest).
- Regenerate capability matrix (`gen_capability_matrix.py --check`) — fails CI if the action list drifted.
- `provided_plugins/cursor-plugin/scripts/test-device-code.ps1` — E2E OAuth smoke test.

