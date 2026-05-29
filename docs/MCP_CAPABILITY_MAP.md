# MCP capability map (short index)

**Authoritative action list:** [MCP_CAPABILITY_MATRIX.md](MCP_CAPABILITY_MATRIX.md) · **Scale glossary:** [MCP_SCALE.md](MCP_SCALE.md) · **Live catalog:** `GET https://agentstack.tech/mcp/actions`

One registered MCP tool — **`agentstack.execute`** — runs batched steps. Each step names an **action** (for example `projects.get_project`, `rag.search`). The platform exposes **332 catalog actions** across **31** domains on the current release line.

## Discovery

| Endpoint | Purpose |
|----------|---------|
| `GET /mcp/discovery` | Protocol metadata and tool schema |
| `GET /mcp/actions` | Full action catalog for agents |
| `POST /mcp` | Execute steps (`agentstack.execute`) |

## Domain highlights

Full management cycles exist for **projects**, **users/RBAC**, **scheduler**, **assets**, **buffs**, **logic**, **data access (FAP)**, **DNA CRUD**, **wallets/payments**, **RAG**, **social/messenger**, **support**, **integrations**, **agents**, **hosting**, **commerce**, **storage**, and **AI Builder manifest** actions.

**Synergies and recipes:** [MCP_SYNERGIES_AND_INSTRUCTIONS.md](MCP_SYNERGIES_AND_INSTRUCTIONS.md) · **Step-by-step flows:** [MCP_FLOWS_AND_SYNERGIES.md](MCP_FLOWS_AND_SYNERGIES.md) · **AI routing:** [plugins/CONTEXT_FOR_AI_MCP.md](plugins/CONTEXT_FOR_AI_MCP.md)

## Model

- **Batch:** multiple steps in one `POST /mcp` with `options.stopOnError`.
- **Async jobs:** `options.async` + `idempotency_key` → poll `GET /mcp/jobs/{job_id}`.
- **OAuth / AI helpers:** documented under `/mcp` in [MCP_OVERVIEW.md](MCP_OVERVIEW.md).

**Next:** [MCP_QUICKSTART.md](MCP_QUICKSTART.md) · [MCP_TOOLS.md](MCP_TOOLS.md)
