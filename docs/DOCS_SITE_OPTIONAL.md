# Optional docs site (Starlight)

The monorepo ships `docs-site/` — an Astro Starlight shell that mirrors `agentstack_repo/docs/` with Pagefind search and Diátaxis sidebar.

## Build

```bash
npm run build --prefix docs-site
```

`prebuild` automatically:

1. Copies `agentstack_repo/docs/` → `docs-site/src/content/docs/` (injects frontmatter)
2. Generates `reference/mcp/*.md` domain stubs from `CAPABILITY_MATRIX.md`

Output: static site under `docs-site/dist/` (~180 pages including MCP domains).

## Local dev

```bash
cd docs-site && npm install && npm run dev
```

## IA

| Sidebar group | Mirror path |
|---------------|-------------|
| Tutorials | `tutorials/` |
| How-to | `how-to/` |
| Reference | `reference/` (+ generated `reference/mcp/`) |
| Explanation | `explanation/` |

Gene: `docs.public.site.gen1` · See [CONTRIBUTING_DOCS.md](CONTRIBUTING_DOCS.md) for the full publish pipeline.
