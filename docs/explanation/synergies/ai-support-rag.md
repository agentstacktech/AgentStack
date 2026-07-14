# Synergy: AI support + RAG

Staff support threads enriched with **RAG collections** for grounded replies.

---

## Flow

1. Ensure RAG collection exists for the project knowledge base.
2. Staff (or agent) reads support inbox thread.
3. Search RAG with user question context.
4. Send reply via support MCP with cited snippets.

---

## MCP example

**Index documentation:**

```json
{
  "action": "rag.document_add",
  "params": {
    "project_id": 42,
    "collection_id": "support-kb",
    "content": "Refund policy: 30 days...",
    "metadata": { "source": "policy.md" }
  }
}
```

**Search at reply time:**

```json
{
  "action": "rag.search",
  "params": {
    "project_id": 42,
    "collection_id": "support-kb",
    "query": "refund window",
    "limit": 5
  }
}
```

**List inbox:**

```json
{
  "action": "social.support.inbox",
  "params": { "project_id": 42, "status": "open" }
}
```

**Send reply:**

```json
{
  "action": "social.support.send",
  "params": {
    "project_id": 42,
    "thread_id": "THREAD_ID",
    "body": "Our refund policy allows 30 days..."
  }
}
```

---

## REST contours

- Support: `/api/support/*` — [support/INTEGRATION_QUICKSTART.md](../../support/INTEGRATION_QUICKSTART.md)
- RAG: documented under [RAG_PLATFORM_GUIDE.md](../../RAG_PLATFORM_GUIDE.md)

---

## SDK

```typescript
const hits = await sdk.protocol.execute({
  action: "rag.search",
  params: { project_id: 42, collection_id: "support-kb", query: userQuestion },
});
```

---

## Caveats

> **Partial:** Automatic AI binding (model picks RAG collection per thread) requires project support AI config in the dashboard — not all tenants have autonomous send enabled.
>
> **Not implemented:** Bi-directional sync from CRM contact notes into RAG without an explicit `rag.document_add` step.
>
> **Rate limits:** `rag.search` and support send endpoints share standard project rate limits.
