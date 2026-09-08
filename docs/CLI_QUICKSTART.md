# AgentStack CLI quickstart

**Genetic tag:** `repo.tooling.user_cli.gen1`  
**Package:** `@agentstack/cli` · bin `agentstack`  
**ADR / gene:** `repo.tooling.user_cli.gen1` — package `@agentstack/cli` in [agentstack-sdk](https://github.com/agentstacktech/agentstack-sdk).

## Install

**Verified today** — clone [agentstack-sdk mirror](https://github.com/agentstacktech/agentstack-sdk), build CLI:

```bash
git clone https://github.com/agentstacktech/agentstack-sdk.git agentstack-sdk
cd agentstack-sdk && npm ci && npm run build -w @agentstack/cli
node packages/cli/dist/bin.js version
node packages/cli/dist/bin.js doctor --json
# → schema: agentstack.cli.doctor.v1
```

**After npm publish (H5):**

```bash
npm i -g @agentstack/cli
agentstack doctor --json
```

Requires Node >= 22.

## Login

**Interactive (Device Code):**

```bash
agentstack auth login
# Open the printed /activate?user_code=… URL, approve, then:
agentstack auth use-project <tenant_id>   # id > 1
```

**CI:**

```bash
export AGENTSTACK_API_KEY=…
export AGENTSTACK_PROJECT_ID=42
agentstack doctor --json
# → schema: agentstack.cli.doctor.v1
```

## Common flows

```bash
# Host (default mini HTML — or --file ./index.html)
agentstack hosting quick-start --name demo
agentstack hosting deploy --bucket <uuid> --dir ./dist
agentstack hosting deploy --bucket <uuid> --zip ./site.zip

# RAG
agentstack rag create-collection --name docs
agentstack rag ingest --collection <id> --file ./README.md
agentstack rag search --collection <id> --q "how to deploy"

# Agents (SDK startRun — CLI verb is `run`)
agentstack agents list
agentstack agents run --id <agent_uuid> --input '{"prompt":"hi"}' --idempotency-key run-1

# Thin SDK aliases (list only — mutate via execute / Studio)
agentstack bots list
agentstack crm contacts --q alice

# Escape hatch (any MCP action)
agentstack execute --action discovery.list --params '{}'
agentstack discover search "deploy static site"

# Tenant 8DNA generation (requires --env-uuid)
agentstack generation diff --env-uuid <uuid>
agentstack generation gates --env-uuid <uuid>
```

## Debug

```bash
agentstack doctor --json
# → schema: agentstack.cli.doctor.v1 --trace
agentstack help hosting quick-start
eval "$(agentstack completion bash)"   # or: agentstack completion powershell
agentstack auth profiles
agentstack auth use-profile ci
```

Exit codes: `0` ok · `1` usage · `2` auth · `3` api · `4` caps

Read-only config (CI): `AGENTSTACK_CONFIG_READONLY=1` refuses writing `~/.agentstack/config.json`.

Idempotency (channel-specific — not interchangeable):
- `execute --idempotency-key K` → MCP `options.idempotency_key`
- `agents run --idempotency-key K` → REST body `idempotency_key` on `startRun`

## Channels

| Task | Channel |
|------|---------|
| hosting / rag / agents / storage / projects / data | SDK REST (`sdk.*`) |
| discover list / execute / generation / keys | MCP `agentstack.execute` |
| discover search / caps | SDK `sdk.mcp.discoverByIntent` / `getDiscovery` |

`generation.*` verbs intentionally use MCP (tenant 8DNA generation plane) — not `sdk.sandbox` shortcuts.

IDE agents should keep using MCP plugins; use this CLI for human terminals and CI.

## Related

- SDK: [agentstack-sdk/AGENTS.md](https://github.com/agentstacktech/agentstack-sdk/blob/master/AGENTS.md)
- Ops tooling (internal monorepo): `platform_tooling.py` — not this product
