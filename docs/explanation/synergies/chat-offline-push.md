# Synergy: Chat offline + push

**Messenger** delta sync keeps threads consistent offline; **Web Push** notifies when the PWA is backgrounded.

---

## Flow

1. Client syncs messenger channels and messages (delta APIs).
2. User subscribes to push (`/api/push/subscribe` from PWA).
3. Agent or logic sends `notifications.send_push` on new DM.
4. User returns via deep link to thread.

---

## MCP example

**Discover channels:**

```json
{
  "action": "social.channels.list",
  "params": { "project_id": 42 }
}
```

**Register push (discovery hint — client still calls REST subscribe):**

```json
{
  "action": "notifications.subscribe_push",
  "params": { "project_id": 42 }
}
```

**Send push:**

```json
{
  "action": "notifications.send_push",
  "params": {
    "project_id": 42,
    "user_id": 1001,
    "title": "New message",
    "body": "You have a new DM",
    "data": { "route": "/messenger/threads/THREAD_ID" }
  }
}
```

**Health check:**

```json
{
  "action": "web_push.get_health",
  "params": {}
}
```

---

## Offline behaviour

See [messenger/OFFLINE_AND_RELIABILITY.md](../../messenger/OFFLINE_AND_RELIABILITY.md).

---

## Caveats

> **Partial:** Push requires user opt-in in a installed PWA; browser tab alone is insufficient on iOS.
>
> **Not implemented:** Server-initiated push for every messenger event without logic/notification rules — configure triggers explicitly.
>
> **Scheduler fallback:** When push fails, queue digest via `scheduler.create_task` (pattern documented in MCP social prompts).
