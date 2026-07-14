# AgentStack Plugins — Skills authoring guide

**Version:** 0.1  
**Date:** 2026-02-24  
**Validation:** Recommended principles: Creation over Conflict, Decomposition, Elegant Minimalism, Time-Decomposition-Completion (see repo documentation).

---

## Purpose

Shared rules and quality examples for skills in Cursor and Claude plugins (gen3). Skills teach decision-first routing to MCP. Legacy gen1 skill names (`agentstack-8dna`, etc.) are retired. **8DNA** in skills is described publicly as **JSON+** (structured JSON with built-in support for variants, e.g. A/B tests), with explicit data store (key-value API, project API); see **docs/architecture/DNA_KEY_VALUE_API.md**. Do not expose architecture details in public skills before patents. Separate skills: **Assets** — assets (commerce, games, inventory); **RBAC** — roles and permissions; **Buffs** — trials, subscriptions, effects; **Payments** — payments and wallets; **Auth** — login, registration, profile.

---

## Required SKILL.md structure

1. **Frontmatter (YAML):** `name`, `description` — required.
2. **When to use** — when the agent should apply this skill (triggers, typical requests).
3. **Capabilities** and/or **Instructions** — what the skill does and step-by-step guidance.
4. **Examples** — concrete examples in "user request → tool/action" format.
5. **References** — links to MCP_CAPABILITY_MATRIX, MCP_QUICKSTART, 8DNA/philosophy docs.

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

- **Repo (docs, philosophy):** in the skill text use neutral references: "See **MCP_CAPABILITY_MATRIX** in repo docs", "See **8DNA_HIERARCHY_EVOLUTION_CAPABILITIES**, **PARADIGM_SHIFT_BIOLOGICAL_COMPUTING** in repo philosophy". When opening from repo: paths `docs/MCP_CAPABILITY_MATRIX.md`, `philosophy/…`.
- **Plugin:** "See **MCP_QUICKSTART.md** in plugin root" — in cursor-plugin or claude-plugin root. Do not use absolute paths; Cursor/Claude supply plugin context.
- **Elegant Minimalism:** do not copy large MCP tables from docs into the skill; short table in skill + link to MCP_CAPABILITY_MATRIX for full list.

---

## Progressive disclosure (reference.md / examples.md)

- **SKILL.md** remains the main carrier; target size up to ~500 lines.
- Optional **reference** companion — move detailed reference only if the tools/params table grows and duplicates MCP_CAPABILITY_MATRIX. Otherwise prefer a link to the doc.
- Optional **examples** companion — move extended scenarios only if SKILL accumulates many examples (e.g. >10); keep 2–3 key ones in SKILL.
- Links from SKILL — one level to those companions. Do not chain reference → other files.

---

## Golden example: agentstack-projects

Reference for structure and tone of an MCP-oriented skill.

| Element | How it's done | Why |
| -------- | ------------- | ----------- |
| **description** | Third person, WHAT (creates/manages projects and API keys via MCP), WHEN (create project, get API key, list, stats…), triggers (project, API key, stats, attach) | Agent reliably picks the skill from the user request. |
| **When to use** | Concrete user phrases and scenarios (anonymous, attach to account) | Clear triggers, no vague wording. |
| **Capabilities** | MCP tools table with short purpose | Quick scan; details in MCP_CAPABILITY_MATRIX. |
| **Instructions** | Numbered steps: first-time, list/inspect, attach, API keys | Complete module (Time-Decomposition-Completion); each step has an outcome. |
| **Examples** | Format ""User says…" → `tool` with params» | Concrete input → action; reproducible. |
| **References** | MCP_CAPABILITY_MATRIX, MCP_QUICKSTART (plugin) | Links instead of copy-paste (Elegant Minimalism). |
| **Tips** | Short constraints (anonymous tier, keys shown once) | Minimal text, only what's needed. |

For the Claude plugin use the same skill but replace "Cursor" with "Claude Code" and ensure links point to claude-plugin artifacts (MCP_QUICKSTART in plugin root).

---

## Pre-commit validation

Before changing a skill, check:

- Creation over Conflict: we create value, not fight alternatives.
- Time-Decomposition-Completion: complete module, no half-finished instructions.
- Decomposition: one skill — one domain.
- Elegant Minimalism: minimal text, links instead of duplication, SKILL.md up to ~500 lines.
- 8DNA / Time: 8DNA skill matches official architecture; versions and CHANGELOG when evolving skills.
