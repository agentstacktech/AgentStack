# Synergy: Manifest sandbox promote

Promote **sandbox** experiments to **production** — either **8DNA generation environments** or **hosting release snapshots**.

---

## Two promote paths

| Plane | MCP action | Use |
|-------|------------|-----|
| **8DNA generation** | `generation.promote` | Sandbox config → production environment |
| **Hosting releases** | `hosting.release.promote` | Snapshot → live site bucket |

Export/import integration manifests for agent tool loops: `integrations.export_connection_manifest`.

---

## Generation promote

**List sandboxes:**

```json
{
  "action": "generation.list",
  "params": { "project_id": 42 }
}
```

**Run gates (optional):**

```json
{
  "action": "generation.gates",
  "params": {
    "project_id": 42,
    "environment": "sandbox-feature-x"
  }
}
```

**Promote:**

```json
{
  "action": "generation.promote",
  "params": {
    "project_id": 42,
    "environment": "sandbox-feature-x",
    "target": "production"
  }
}
```

---

## Hosting release promote

**Snapshot:**

```json
{
  "action": "hosting.release.snapshot",
  "params": {
    "project_id": 42,
    "bucket_id": "BUCKET_ID"
  }
}
```

**Promote:**

```json
{
  "action": "hosting.release.promote",
  "params": {
    "project_id": 42,
    "bucket_id": "BUCKET_ID",
    "release_id": "RELEASE_ID"
  }
}
```

---

## Integration manifest

```json
{
  "action": "integrations.export_connection_manifest",
  "params": { "project_id": 42 }
}
```

Import to sandbox project with `integrations.import_connection` before promoting generation environment.

---

## Caveats

> **Solid:** `hosting.release.promote` for static/SPA sites.
>
> **Partial:** `generation.promote` runs promotion checks — manual approval steps may block automation.
>
> **Not implemented:** Single-click promote of **commerce storefront manifest** together with generation environment — run Storefront Studio apply separately after promote.
