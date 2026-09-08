# Plugin version policy

**Genetic tag:** `repo.plugins.publication_gates.gen1`  
**Platform SoT:** `shared/constants.py` → `AGENTSTACK_CORE_VERSION`

Cursor, Claude, and GPT plugin manifests must match the platform patch line unless a grant-scoped specialty plugin documents an exception.

## Owner gate (default)

**Do not bump** plugin semver (`plugin.json`, `marketplace.json`, `TARGET_VERSION` in `validate-plugin.mjs`) unless Lance explicitly orders it (`повысь версию`, `bump to 0.4.x`, `release 0.4.x`). `сделай всё что нужно` is **not** consent.

Ship on the **current** version. Add CHANGELOG bullets under that section. Do not invent `0.4.x+1` to satisfy a validator, and do not **downgrade** a version already on `origin`.

Platform axiom: `axiom.version.control.lance_will.gen2` · plugin maintainers index in the publish repo.

## Bump rules (Cursor plugin)

*Only after the owner gate above.*

| Change type | Semver | Examples |
|-------------|--------|----------|
| Patch | `0.4.x` → `0.4.x+1` | Skill/copy/screenshot polish, hook bugfix, docs, MCP dedupe (0.4.16) |
| Minor | `0.x.0` | New UX layer surface, MCP auth contract change, new required hook event |
| Major | `x.0.0` | Breaking install/auth for consumers |

## Release SOP

1. Align `provided_plugins/cursor-plugin/plugins/agentstack/.cursor-plugin/plugin.json` `version` with platform line (or document exception).
2. Add `CHANGELOG.md` section for that version.
3. `node provided_plugins/scripts/sync-plugin-kernel.mjs --check`
4. `node provided_plugins/scripts/audit-cursor-plugin.mjs --strict-screenshots`
5. Sync publish artifact: `node provided_plugins/scripts/sync-cursor-plugin-publish.mjs ../cursor-plugin-publish`
6. Tag publish repo `v<version>` (must equal `plugin.json` version).
7. Resubmit / await Cursor Marketplace review for listing updates (manual review applies to updates too).

```bash
node scripts/codegen-plugin-versions.mjs
node scripts/codegen-plugin-versions.mjs --check   # CI
```

## Pre-release checklist

- [ ] No version drift (plugin.json = CHANGELOG = TARGET_VERSION in validate-plugin)
- [ ] `listing.json` (not Cursor multi-plugin `marketplace.json`)
- [ ] Screenshots 1920×1200
- [ ] Device Code self-contained (`lib/plugin-kernel`)
