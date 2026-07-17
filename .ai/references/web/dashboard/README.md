# References — Web / Dashboard

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Admin/dashboard reference material: table/list/detail patterns, bulk-action UX, role/permission-gated UI notes, data-visualization patterns, and export/reporting UX.

## Supported file types

Markdown (`.md`) for patterns/notes; small code snippets in fenced blocks; images (`.png`/`.svg`) for layouts (linked where large).

## Naming conventions

`kebab-case` by concern (`data-table.md`, `bulk-actions.md`, `role-gated-ui.md`).

## How agents should use it

Read when building dashboard features, alongside `../../../skills/backend/backend-authorization` and `../../../skills/security/authorization-security`. UI gating is convenience — the **server enforces** role/ownership/tenant access.

## References are not requirements

Material here is **context, not commitment.** A dashboard reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
