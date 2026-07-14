# Contributing to public docs

English-only mirror: `agentstack_repo/docs/`. Internal authoring: monorepo `docs/` per `PUBLIC_DOCS_MANIFEST.md` (monorepo only).

## Release gate (run before publish)

From monorepo root:

```bash
node scripts/audit-public-docs-pipeline.mjs
```

Runs MCP matrix check, OpenAPI check, policy + links, stats placeholders, capability lockfile, plugin versions, Claude skill stubs, and docs-site build.

## When you change MCP or REST surface

1. Regenerate the MCP capability matrix (monorepo generator + public redact step).
2. Regenerate the OpenAPI bundle if routes changed.
3. Refresh platform stats snapshot and patch `<!-- stats:* -->` placeholders.
4. Refresh the plugin capability lockfile.
5. `npm run audit:public-docs --prefix agentstack-frontend`

See monorepo `docs/PRE_PUBLISH_CHECKLIST.md` for exact command names.

## When you change monorepo `docs/` sources

```powershell
.\scripts\sync_agentstack_repo.ps1
```

Manifest-driven allowlist — the sync **never** copies the monorepo MCP matrix stub (regenerates instead).

## Docs site (Starlight)

```bash
npm run build --prefix docs-site
```

`prebuild` syncs this mirror and generates MCP domain reference stubs.

## Plugins

```bash
node provided_plugins/scripts/audit-cursor-plugin.mjs
node provided_plugins/scripts/sync-claude-skill-stubs.mjs
```

CI: `.github/workflows/public-docs-parity.yml`, `.github/workflows/plugin-audit.yml`.

**Public doc owners:** genetic tags `docs.public.*.gen1` (engineering genes in the monorepo).
