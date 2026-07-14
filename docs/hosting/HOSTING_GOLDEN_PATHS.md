# Hosting golden paths

How builders reach a **live public URL** from acquisition surfaces to **Storage → Sites**. Product promise: **honest 2-click** publish for signed-in users with a project.

---

## Phase 1 — live (signed-in)

| Step | Action | Outcome |
|------|--------|---------|
| 1 | Account + project | Authenticated builder context |
| 2 | **Storage → Sites** → create → **Publish** | `https://agentstack.tech/s/{project_id}/{bucket_name}/` |

Alternate entries (same terminus):

- [/host-site](https://agentstack.tech/host-site) funnel → Sites hub
- Compass ⌘K playbooks (`host-*`)
- MCP `hosting.site.quick_start`
- Discovery Hub → hosting cluster

---

## Acquisition surfaces → Sites

| Entry | Route / trigger | Lands on |
|-------|-----------------|----------|
| Marketing home | `/host-site` CTA | Auth → Sites |
| For developers | `/for-developers` host CTA | Sites |
| Demo pool | `/demo` | Anonymous TTL URL (limits apply) |
| Project overview | Host ladder CTA | `/projects/:id/storage/sites?deploy=1` |
| Compass | `host-and-sell-15` playbook | Sites + optional Storefront Studio |
| Cmd+K | `G Y` / hosting nav | `/storage/sites` |
| MCP / SDK | `hosting.site.quick_start` | Bucket + optional publish URL |
| Storage import | Folder → site bucket | `hosting.storage.import_folder` |
| AI Builder | Publish to Sites | Sites hub (when enabled) |

---

## Honest limits

- **Not** zero-click: you need a project and publish permission (`write` upload, `admin` promote releases).
- **Guest paste→publish** (TTL URL without account) is a separate promo phase — rate limits and security review apply.
- Site bytes count toward the **owner storage quota** — [subscription/SUBSCRIPTION_TIERS.md](../subscription/SUBSCRIPTION_TIERS.md).
- On-platform AI site editing is **in development** — [ROADMAP_INTEGRATOR.md](../ROADMAP_INTEGRATOR.md).

---

## MCP quick reference

| Action | Use |
|--------|-----|
| `hosting.site.quick_start` | Create bucket + upload + publish |
| `hosting.deploy_files` | Batch upload (max 50 files per call) |
| `hosting.release.snapshot` | Immutable snapshot |
| `hosting.release.promote` | Promote snapshot to live |
| `hosting.project.status` | Host/Sell/Scale ladder — [PROJECT_HOSTING_PLANE.md](PROJECT_HOSTING_PLANE.md) |

---

## After host: sell ladder

Post-publish Compass nudges **Storefront Studio** and project wallet — see [commerce/STOREFRONT_STUDIO.md](../commerce/STOREFRONT_STUDIO.md).

**Tutorial:** [tutorials/01_hosting_static_site.md](../tutorials/01_hosting_static_site.md)

**Next:** [HOSTING_QUICKSTART.md](HOSTING_QUICKSTART.md) · [PROJECT_HOSTING_PLANE.md](PROJECT_HOSTING_PLANE.md)
