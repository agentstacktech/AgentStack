# Work Graph — what it gives you

**Genetic tags:** `core.agents.work_graph.gen1` · `core.mcp.instruction_plane.gen1` · `frontend.promo.dual_audience_funnel.gen1`  
**Public page:** https://agentstack.tech/work-graph  
**Contract:** [MCP_AGENT_OPERATING_CONTRACT.md](./MCP_AGENT_OPERATING_CONTRACT.md)

Work Graph is the control plane on the same backend. One MCP tool (`agentstack.execute`) still runs every action. After the session is bound to a project, the agent does not shop the catalog. It calls `agents.work_next` and does what `packet.next_action` says.

The server holds the plan. Each turn carries one step: claim it, do it, check it, ask for the next one. Idle is a valid answer. Blocked names the unblock.

## Measured on 2026-10-07

Local registry build. Not a production traffic sample. Not a provider tokenizer.

| Payload | UTF-8 bytes | Rough tokens (bytes ÷ 4) |
|---------|-------------|---------------------------|
| Example `next_action` plus two alternatives (`agents.plan_claim`) | 250 | ~63 |
| `discovery.search` compact page, first 20 rows | 8,889 | ~2,222 |
| Handshake contract JSON, once (`en-US`) | 11,297 / 16,384 cap | ~2,824 |
| Compact page, 50 rows (HTTP max) | 20,852 | ~5,213 |
| Full registry catalog JSON | 1,680,071 (795 actions) | ~420,018 |

Contract prose the same run: **7,258 / 8,192** characters. `server_discover_instructions()`: **1,205** characters.

The full catalog is **6,720×** the example packet (1,680,071 / 250). A 20-row search page is **35.6×** that packet (8,889 / 250).

Method:

```text
build_ai_prompt_contract("en-US")
compact_catalog_row on the first 20 and 50 rows of the registry catalog
project_next_action for agents.plan_claim plus knowledge.search and storage.list_files
```

Bytes ÷ 4 is an English size estimate. It is not tiktoken and not an invoice.

## Operator targets (not observed rates)

| Signal | Target |
|--------|--------|
| Autonomous loops that follow `packet.next_action` | ≥ 90% (WG-SLO-03) |
| Catalog shopping while a packet is already present | ≤ 5% (WG-SLO-04) |

Source: [WORK_GRAPH_SLO.md](../operations/WORK_GRAPH_SLO.md). These are steady-production goals. This page does not plot a live counter.

## What you get, and why

| Who | Without the graph | With the graph |
|-----|-------------------|----------------|
| Founder with open loops | Every new chat re-reads the tool menu and restarts the briefing | The outcome is written once. The next chat takes the head of the queue |
| A person with more tasks than hours | The agent invents a tool and spends the window choosing | The list says ready, blocked, or idle. Blocked names the unblock |
| A business that needs the work finished | Thrash between catalog, guess, and retry | Claim, execute, verify, on the same project as the site and the payments |
| Someone wiring an agent | Paste the catalog into the prompt “so it can see the tools” | `agents.work_next`, then `packet.next_action`. Search is for a schema when there is no packet |

The saving is the context you do not load. The reliability is one executable step instead of a ranked menu. The business effect is that the turn is spent on the step, not on discovering that the step exists.

## Principles

- **Creation over Conflict.** One next action. No second workflow engine and no second catalog inside the prompt.
- **Elegant Minimalism.** The handshake stays inside an 8 KiB prose budget. The decision object is hundreds of bytes.
- **Observability first.** Byte counts are a dated local build. Follow-rate figures are targets. They are not added into one savings claim.

## Loop

```text
session → context.project_id → agents.work_next → packet.next_action → claim / execute → work_next
```

Recipe: `mcp_work_loop_v1`. Prompt: `agentstack_closed_loop_autonomy`.
