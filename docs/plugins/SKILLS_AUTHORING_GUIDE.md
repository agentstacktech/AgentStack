# AgentStack Plugins — Skills authoring guide

**Version:** 0.1  
**Date:** 2026-02-24  
**Validation:** Keep skills **focused** (one domain per skill), **complete** (no half-finished flows), **short** (link to docs instead of pasting large tables), and aligned with published **docs** in this repository.

---

## Purpose

Shared rules and quality examples for skills in Cursor and Claude plugins. Skills teach the agent when and how to use the AgentStack ecosystem (8DNA, Projects, Rules Engine, **Assets**, **RBAC**, **Buffs**, **Payments**, **Auth**, MCP tools). **8DNA** in skills is described publicly as **JSON+** (structured JSON with built-in support for variants, e.g. A/B tests), with explicit data store (key-value API, project API); see **docs/architecture/DNA_KEY_VALUE_API.md**. Do not expose architecture details in public skills before patents. Separate skills: **Assets** — assets (commerce, games, inventory); **RBAC** — roles and permissions; **Buffs** — trials, subscriptions, effects; **Payments** — payments and wallets; **Auth** — login, registration, profile.

---

## Required SKILL.md structure

1. **Frontmatter (YAML):** `name`, `description` — required.
2. **When to use** — when the agent should apply this skill (triggers, typical requests).
3. **Capabilities** and/or **Instructions** — what the skill does and step-by-step guidance.
4. **Examples** — concrete examples in "user request → tool/action" format.
5. **References** — links to **MCP_SERVER_CAPABILITIES**, **MCP_QUICKSTART**, and **docs/architecture/DNA_KEY_VALUE_API.md** where 8DNA is relevant.

Optional: **Tips**, **Flow**, **Core concepts** — as needed, without bloat.

---

## Description requirements

- **Third person:** description goes into system context; not "I can…" / "You can…", but "Does X", "Use when…".
- **WHAT + WHEN:** what the skill does and for which requests to use it.
- **Trigger words:** include key terms (MCP group names, domain: projects, 8DNA, rules, API key, etc.).

### Wording examples

**Good:**

```yaml
description: Creates and manages AgentStack projects and API keys via MCP (projects.*). Use when the user wants to create a project, get an API key, list or inspect projects, get stats, manage users or settings, or attach an anonymous project to an account.
```

Why: third person, WHAT (creates/manages projects and API keys via MCP), WHEN (wants to create…, list…, get stats…), triggers (projects, API key, stats, attach).

**Bad:**

```yaml
description: Helps with AgentStack.
```

Why: no WHEN, no triggers, vague WHAT.

---

## Link rules

- **This docs repo:** reference files under `docs/` only (e.g. `docs/MCP_SERVER_CAPABILITIES.md`, `docs/architecture/DNA_KEY_VALUE_API.md`). Do not point skills at paths that are not published here.
- **Plugin bundle:** "See **MCP_QUICKSTART.md** in plugin root" — in cursor-plugin or claude-plugin root. Do not use absolute paths; Cursor/Claude supply plugin context.
- **Keep skills small:** do not copy large MCP tables from docs into the skill; use a short table + link to **MCP_SERVER_CAPABILITIES** for the full list.

---

## Progressive disclosure (reference.md / examples.md)

- **SKILL.md** remains the main carrier; target size up to ~500 lines.
- **reference.md** (optional in your plugin) — detailed tables only if they exceed [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).
- **examples.md** (optional) — extended scenarios when SKILL has many examples (>10).
- Link one level from SKILL to companion files or AgentStack doc URLs — avoid deep chains.

---

## Golden example: agentstack-projects

Reference for structure and tone of an MCP-oriented skill.

| Element | How it's done | Why |
| -------- | ------------- | ----------- |
| **description** | Third person, WHAT (creates/manages projects and API keys via MCP), WHEN (create project, get API key, list, stats…), triggers (project, API key, stats, attach) | Agent reliably picks the skill from the user request. |
| **When to use** | Concrete user phrases and scenarios (anonymous, attach to account) | Clear triggers, no vague wording. |
| **Capabilities** | MCP tools table with short purpose | Quick scan; details in MCP_SERVER_CAPABILITIES. |
| **Instructions** | Numbered steps: first-time, list/inspect, attach, API keys | Complete module; each step has an outcome. |
| **Examples** | Format ""User says…" → `tool` with params» | Concrete input → action; reproducible. |
| **References** | MCP_SERVER_CAPABILITIES, MCP_QUICKSTART (plugin) | Links instead of copy-paste. |
| **Tips** | Short constraints (anonymous tier, keys shown once) | Minimal text, only what's needed. |

For the Claude plugin use the same skill but replace "Cursor" with "Claude Code" and ensure links point to claude-plugin artifacts (MCP_QUICKSTART in plugin root).

---

## Pre-commit validation

Before changing a skill, check:

- **Value:** the skill should help the user accomplish a task, not argue with other approaches.
- **Completeness:** no half-finished instructions; each advertised flow should be runnable.
- **Scope:** one skill — one domain.
- **Size:** minimal text, links instead of duplication; **SKILL.md** up to ~500 lines.
- **Accuracy:** 8DNA and API wording match **docs/**; bump skill version notes when behaviour changes.
