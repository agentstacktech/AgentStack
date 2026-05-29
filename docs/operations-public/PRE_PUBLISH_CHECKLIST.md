# Pre-publish checklist (maintainers)

1. Update monorepo `docs/` sources
2. Update [PUBLIC_DOCS_MANIFEST.md](https://github.com/agentstacktech/AgentStack/blob/master/docs/PUBLIC_DOCS_MANIFEST.md) if needed
3. `.\scripts\sync_agentstack_repo.ps1`
4. Run the public docs policy check from the AgentStack monorepo (see [CONTRIBUTING_PUBLIC_DOCS.md](https://github.com/agentstacktech/AgentStack/blob/master/docs/CONTRIBUTING_PUBLIC_DOCS.md))
5. `node scripts/check-doc-links.mjs`
6. Manual read [BUILD_YOUR_PRODUCT.md](../BUILD_YOUR_PRODUCT.md)
7. Copy `agentstack_repo/*` to GitHub AgentStack root
8. Commit `docs: …` on `master`
9. [DOCS_VERIFY_LINKS_CHECKLIST](https://github.com/agentstacktech/AgentStack/blob/master/docs/DOCS_VERIFY_LINKS_CHECKLIST.md)
10. Plugin URL audit (MCP_CAPABILITY_MATRIX, plugins README)
