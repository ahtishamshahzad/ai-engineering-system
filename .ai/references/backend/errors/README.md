# References — Backend / Errors

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Error-handling reference material: the error taxonomy (operational vs programmer), the single safe response shape, status-code mapping tables, and central-handler patterns (Express middleware / Nest exception filters).

## Supported file types

Markdown (`.md`) for taxonomy/mapping; small code snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`error-taxonomy.md`, `status-mapping.md`, `error-shape.md`).

## How agents should use it

Read when implementing/reviewing error handling, alongside `../../../skills/backend/backend-error-handling`. One safe response shape everywhere; internals never leak to clients.

## References are not requirements

Material here is **context, not commitment.** An error-handling reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
