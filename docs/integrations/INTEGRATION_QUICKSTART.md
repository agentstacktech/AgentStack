# Integration Hub quickstart

1. List recipes: MCP `integrations.list_recipes`
2. Install: `integrations.install_recipe` with `project_id` and `recipe_id`
3. Configure connection secrets in dashboard **Integrate** workspace (`/user/projects/:id/integrate`)
4. Verify inbound signature — [../security/WEBHOOK_SECURITY.md](../security/WEBHOOK_SECURITY.md)

```json
{
  "steps": [{
    "id": "install",
    "action": "integrations.install_recipe",
    "params": { "project_id": 42, "recipe_id": "stripe_payment_succeeded" }
  }]
}
```

**Next:** [../api/integrations.md](../api/integrations.md) · [../PARITY_MATRIX.md](../PARITY_MATRIX.md)
