# Code tabs convention (curl · SDK · MCP)

Public quickstarts use three channels with the **same step**:

| Tab | When to use |
|-----|-------------|
| **curl** | Smoke tests, CI, language-agnostic |
| **SDK** | TypeScript `@agentstack/sdk` / `sdk.protocol` |
| **MCP** | `agentstack.execute` steps |

On the [docs site](../DOCS_SITE_OPTIONAL.md), Starlight `<Tabs>` groups these blocks. Example structure:

```markdown
### REST (curl)
... bash block ...

### MCP
... json block ...

### SDK
... typescript block ...
```

Gene: `docs.public.site.gen1`
