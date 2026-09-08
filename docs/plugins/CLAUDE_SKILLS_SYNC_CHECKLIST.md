# Partner plugin — skills sync checklist (Cursor gen3 → Claude / VS Code)

**When:** After any change under `provided_plugins/cursor-plugin/plugins/agentstack/skills/`.  
**Source of truth:** Cursor gen3 (`repo.plugins.cursor.gen3`).  
**Shared lib:** `provided_plugins/scripts/lib/plugin-skill-parity.mjs` + `plugin-skill-sync.mjs`.

## Automated (preferred)

```bash
# Sync + prune legacy gen1 alias folders
node provided_plugins/scripts/sync-claude-skill-stubs.mjs
node provided_plugins/scripts/sync-vscode-skill-stubs.mjs

# CI / release gates
node provided_plugins/scripts/sync-claude-skill-stubs.mjs --check
node provided_plugins/scripts/sync-vscode-skill-stubs.mjs --check
node provided_plugins/scripts/check-claude-skills-parity.mjs
node provided_plugins/scripts/check-vscode-skills-parity.mjs

# Triangle DX plane (includes stub --check)
npm run audit:agentstack-dx-plane
```

**GPT** has no skill tree — run `node provided_plugins/scripts/sync-gpt-instructions.mjs` when `agentstack-backend/SKILL.md` router table changes.

## Manual steps (if not using sync scripts)

1. Copy `cursor-plugin/plugins/agentstack/skills/<name>/` → `claude-plugin/skills/<name>/` and/or `vscode-plugin/skills/<name>/`.
2. Adapt product name in body (automated: Claude Code / VS Code).
3. Retire gen1 folders (`agentstack-8dna`, `agentstack-payments`, …) when canonical gen3 stub exists.
4. Update plugin `CHANGELOG.md` and [CLAUDE_VS_CURSOR_PLUGIN.md](CLAUDE_VS_CURSOR_PLUGIN.md).

## Skip mirror (Cursor-only)

| Skill | Reason |
|-------|--------|
| `agentstack-backend` | Meta-router; feeds GPT instructions autogen |
| `solana` | Grant-scoped optional skill |

Hooks and Device Code remain **Cursor-only**.

**Genetic tags:** `docs.plugins.claude_skills_sync.gen1` · `repo.plugins.vscode.gen1`
