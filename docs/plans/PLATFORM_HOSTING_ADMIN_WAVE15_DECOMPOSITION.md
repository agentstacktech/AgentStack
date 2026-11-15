# Platform hosting admin + deploy hygiene — Wave 15

**Philosophy:** Elegant Minimalism (reuse `listFiles` / `bulkDelete` / `hosting.gc.orphans`, no second file store) · Observability first (MCP ladders + deploy fixture drift)

**Genes:** `docs.golden_paths.hosting.gen1` · `core.hosting.bucket.gen1` · `core.mcp.self_description.gen1` · `repo.ops.vps_deploy.gen1` · `frontend.admin.operator_chrome.gen1`

---

## Shipped (this pass)

| ID | What | Where |
|----|------|-------|
| W15-A | Admin **Hosting files** panel | `AdminHostingFilesPanel.tsx` → Hosting health |
| W15-B | Prefix-scoped bucket scan | `hosting_ops._site_file_entries_under_prefix` |
| W15-C | `q` min length 2 on list | `hosting_static_upload_service.list_files` |
| W15-D | `ai.chat.settings.*` fixtures | `fixtures/capabilities/ai_builder.json` |
| W15-E | MCP ladder text | `hosting_instruction_atoms` |

---

## P0 — Deploy

1. `check_descriptor_fixture_drift` green (reply_source + ai.chat).
2. `RebuildBackend` — core/shared journals not on VPS until deploy.
3. Frontend: `git pull agentstack dev` then push `c6549135+` for hosting admin panel.

---

## P1 — Hosting list perf (remaining)

### P1-1 Full-bucket explorer without filter

**Problem:** `useBucketExplorer` still calls `listFiles` with no `q` → O(files) rglob.

**Where:** `useHostingQueries.ts` · `hosting_static_upload_service.py`

**Option A (KISS):** Lazy tree — only `prefix` per expanded folder (align with admin panel).

**Option B:** Background manifest index leaf `ecosystem.hosting.file_index` (heavier — ADR first).

**Example (A):**
```ts
// useHostingFiles: pass prefix from tree selection, never fetch whole bucket at once
sdk.hosting.listFiles(pid, bid, { prefix: expandedPath, limit: 200 });
```

---

## P1 — Admin UX

| Task | Detail |
|------|--------|
| W15-F | Deep-link `?project_id=&bucket=` on Hosting health |
| W15-G | Show `hosting.quota.breakdown` beside file panel |
| W15-H | Wire `hosting.site.prune` ladder in UI (releases before bulk delete live tree) |
| W15-I | i18n `admin` locale keys for `hosting_files_*` (EN fallbacks exist) |

---

## P2 — Ecosystem self-build (substrate)

See user ask: backend self-build **not** via tenant hosting git.

| Layer | Safe path |
|-------|-----------|
| Source | Private monorepo or `agentstack-core`/`shared` journal → CI tar |
| Build | Docker `agentstack-backend` on trusted runner |
| Promote | `deploy-from-windows.ps1` only; no `script.run` on VPS with docker.sock |
| Secrets | Never in git journal; `.env.production` on VPS only |

**Anti-pattern:** `git push agentstack` for Python substrate — hosting build YAML cannot produce backend image.

---

## Test matrix

```bash
pytest agentstack-core/tests/hosting/test_hosting_list_files_q.py -q
npx vitest run src/modules/admin/pages/platform/__tests__/AdminHostingFilesPanel.test.tsx
python agentstack-core/scripts/check_descriptor_fixture_drift.py
```