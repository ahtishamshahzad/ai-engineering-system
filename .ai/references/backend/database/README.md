# References — Backend / Database

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Database reference material (as consumed by the backend): schema/ER notes, data-layer patterns (Prisma/Drizzle/Mongoose), migration sequencing playbooks, indexing/query-plan analyses, and transaction/concurrency patterns. **No real data or credentials.**

## Supported file types

Markdown (`.md`) for notes/analyses; small code/SQL snippets in fenced blocks; ER sketches.

## Naming conventions

`kebab-case` by concern (`schema-notes.md`, `migration-playbook.md`, `index-analysis.md`).

## How agents should use it

Read when working on data access from the backend, alongside `../../../skills/database/README.md`. Coordinate schema/migration changes through the single database engineer; the security lens is `../../../skills/security/database-security`.

## References are not requirements

Material here is **context, not commitment.** A database reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope. **Never store real data or secrets here.**
