# SDK recipes (AI Application Factory)

Each recipe: **Task → Prerequisites → Code → Failures**

## R1 — Static site (hosting)

```typescript
import { AgentStackSDK, resolveAgentStackApiBase } from '@agentstack/sdk';

const sdk = new AgentStackSDK({ apiBase: resolveAgentStackApiBase() });
await sdk.platform.auth.login({ email: process.env.AGENTSTACK_EMAIL!, password: process.env.AGENTSTACK_PASSWORD! });

const result = await sdk.hosting.quickStart({
  project_id: Number(process.env.PROJECT_ID),
  html: '<!doctype html><html><body><h1>Hello</h1></body></html>',
  bucket_name: 'demo',
  publish: true,
});
console.log(result.url);
```

**Failures:** `hosting_gate_failed` → tier; 401 → login.

## R2 — CRUD via REST + DNA

```typescript
const projects = await sdk.platform.api.getProjects();
const created = await sdk.platform.api.createProject({ name: 'AI Demo', description: 'via SDK' });
```

## R3 — Shop (commerce)

```typescript
const offers = await sdk.commerce.discovery.listOffers({ projectId: 1 });
```

## R4 — Support inbox

```typescript
const inbox = await sdk.support.listInbox({ projectId: 1 });
```

## R5 — Logic rule (via command bus)

```typescript
await sdk.protocol.executeCommand({
  command_type: 'logic',
  command_name: 'execute',
  payload: { rule_id: 'my_rule', input: {} },
});
```

**Tutorials:** [../tutorials/](../tutorials/) · **Hosting:** [../hosting/HOSTING_QUICKSTART.md](../hosting/HOSTING_QUICKSTART.md)
