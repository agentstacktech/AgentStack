# Support — AI vs human handoff

## States (product language)

Tickets move between **open**, **waiting**, **resolved**, and reopen flows. Exact enums live in OpenAPI schemas — consume **`GET /api/support/config`** for project defaults.

## AI modes

Projects may enable AI-first replies with escalation:

- AI may respond immediately when confidence is high.
- Silence timers may trigger AI follow-up (`ai_after_silence` style policies).
- Users may **request human** explicitly — MCP exposes `social.support.request_human`.

## Staff tools

Staff consoles keep audit trails on transitions rather than injecting synthetic system rows into user-visible chat when avoidable — rely on REST/MCP transition actions for lifecycle truth.

## Identifiers

Support threads use stable **project-scoped channel identifiers** — treat them as opaque strings in integrations; do not parse internal prefixes.
