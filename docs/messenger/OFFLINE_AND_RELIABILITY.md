# Messenger — offline & reliability (integrator view)

## What we optimize for

- **Fast first paint:** conversations can hydrate from local cache before the network round-trip completes when the user has visited before.
- **Consistent ordering:** messages appear in one timeline order across devices at the product level.
- **Resilience:** transient network failures retry; obvious failures surface in UI rather than silent drop.

## What we do not promise in this guide

This document avoids implementation identifiers (storage backends, internal metrics). Treat the platform as a **black box** with the behaviors above.

## Optimistic UI + server reconcile

Show sent messages immediately in your UI, then reconcile when the server timeline confirms or corrects order. On reconnect, fetch delta/history and merge into local state — same pattern as modern local-first chat apps.

## Merge & edits

Concurrent edits to mutable fields are merged **on the client** so rapid multi-device typing does not corrupt thread state; the server relays updates rather than acting as a manual merge authority for those planes.

## Push notifications

Private mobile/browser notifications use **Web Push** when enabled; in-app live updates use the active session path. See [../publication context — Web Push article](https://github.com/agentstacktech/AgentStack/blob/master/docs/publication/ARTICLE_24_WEB_PUSH_DM_RELIABILITY.md) on the main docs repo for narrative detail.
