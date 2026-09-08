# Масштаб платформы AgentStack (публичные факты)

**На дату:** 20.08.2026 · **Линия Core:** <!-- stats:platform_version -->0.4.18<!-- /stats:platform_version -->  
**Назначение:** LinkedIn, маркетплейсы плагинов, инвесторские слайды — **цифры не править вручную**; обновлять через codegen цепочку stats (`docs.freshness.living.gen1`).

**English:** [PLATFORM_SCALE.md](PLATFORM_SCALE.md)

**SoT для мейнтейнеров:** `docs/publication/platform-stats.snapshot.json` в default branch.

---

## Цифры в одну строку

| Показатель | Значение | Пояснение |
|------------|--------:|-----------|
| **Действий в каталоге** | **<!-- stats:total_actions -->568<!-- /stats:total_actions -->** | `GET /mcp/actions` / Total actions в capability matrix |
| **Доменов действий** | **<!-- stats:mcp_domains -->48<!-- /stats:mcp_domains -->** | Секции доменов в matrix (codegen) |
| **Инструментов в реестре** | **<!-- stats:registry_tools -->593<!-- /stats:registry_tools -->** | `MCP_TOOLS_REGISTERED` = `len(MCP_TOOLS_REGISTRY)`; не catalog actions |
| **Точка входа для IDE** | **1** | `agentstack.execute` — батч шагов, discovery через `/mcp/actions` |
| **Поверхности плагинов** | **4** | Cursor, Claude Code, GPT, VS Code |

**Короткая формулировка для постов:** *550+ действий для агентов* · *45+ доменов* · *один протокол для людей и ИИ*.

---

## Терминология счётчиков MCP

| Метрика | Пример | Назначение |
|---------|--------:|------------|
| **Действия каталога (public)** | <!-- stats:total_actions -->568<!-- /stats:total_actions --> | Маркетинг, `GET /mcp/actions` |
| **Инструменты реестра (runtime)** | ~568 | `GET /mcp/health` → `tools_count` |
| **MCP tools registered (константа)** | <!-- stats:registry_tools -->593<!-- /stats:registry_tools --> | `MCP_TOOLS_REGISTERED` при codegen |

Не обновлять SEO по `tools_count` без сверки с catalog (MET-01).

---

## Крупнейшие домены (июль 2026)

| Домен | Действий | Зачем |
|-------|--------:|-------|
| `social` | 83 | Мессенджер, ленты, каналы |
| `integrations` | 48 | Integration Hub, webhooks, recipes |
| `agentnet` | 43 | Ledger, bridge, rails |
| `commerce_rest` | 39 | Commerce REST |
| `agents` | 25 | Agents Fleet |
| `logic` | 19 | Logic Engine |
| `hosting` | 18 | Sites, publish |
| `crm` | 17 | CRM tissue |

Полная matrix: [MCP_CAPABILITY_MATRIX.md](../MCP_CAPABILITY_MATRIX.md).

---

## Как проверить (prod/staging)

```http
GET https://agentstack.tech/mcp/health
GET https://agentstack.tech/mcp/actions
```

---

**Genetic tag:** `docs.publication.platform_scale.gen1` · Living Plane: `docs.freshness.living.gen1`
