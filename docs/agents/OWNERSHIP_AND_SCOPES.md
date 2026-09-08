# Agents — ownership & scopes

**Platform line:** <!-- stats:platform_version -->0.4.18<!-- /stats:platform_version --> — scoped REST only (no flat global agents list).

## Project agents

Owned by a **project** you administer. They appear in **Dashboard → Agents** (`module=agents`) when your shell exposes that module.

## Personal agents

Owned by your **user** row (“My agents” on profile). Same AgentSpec shape; listings use **`/api/users/me/agents`**.

## Permissions

Creating or deleting agents typically requires **`agents_admin`** capability; running agents requires **`agents_run`**. Matrix rows document each action.

## Integration tip

Always pass explicit **scope** parameters your SDK version expects (`owner=project|user`) so listings never leak across tenants.
