# Mobile and PWA

## Mobile apps

Use `@agentstack/sdk` from React Native or Expo with the same `resolveAgentStackApiBase()` and `sdk.platform.auth`. Prefer **`sdk.messenger`** embed helpers for chat surfaces.

## PWA / installable web

Hosted AgentStack apps can be installable; **Web Push** for DMs requires user permission and a registered service worker on your origin when self-hosting UI. On agentstack.tech, push is wired for messenger when enabled.

**Related:** [messenger/OFFLINE_AND_RELIABILITY.md](../messenger/OFFLINE_AND_RELIABILITY.md) · [auth/OAUTH_DEVICE_FLOW.md](../auth/OAUTH_DEVICE_FLOW.md)

## App updates (PWA)

When you ship a new static build to **hosting** (`/s/{project_id}/{bucket}/`):

1. Deploy files via `hosting.deploy_files` or the Storage → Sites UI.
2. Browsers cache the service worker and shell — bump `cacheVersion` in your web app manifest or change asset hashes so clients fetch the new bundle.
3. For messenger push, re-register push subscription only if your VAPID keys rotate.

**Benefit:** Same project hosting plane as commerce storefronts — no separate CDN project.
