# References — Backend / Authorization

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Authorization reference material: the endpoint × authorization-level matrix, role/permission models, ownership/tenant scoping patterns, object-level rules, and the negative-test catalogue (cross-user, non-admin→admin, untrusted-ID, cross-tenant).

## Supported file types

Markdown (`.md`) for matrices/patterns; small code snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`authorization-matrix.md`, `ownership-scoping.md`, `role-model.md`).

## How agents should use it

Read when designing/reviewing access control, alongside `../../../skills/backend/backend-authorization`, `ownership-authorization`, and `../../../skills/security/authorization-security`. Scope derives from the session, never from client-supplied IDs.

## References are not requirements

Material here is **context, not commitment.** An authorization reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
