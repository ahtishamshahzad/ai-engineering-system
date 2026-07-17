# References — Mobile / State

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

State-management reference material: where each value lives (local/form/shared/server/persisted/navigation), store setup patterns, server-state (RTK Query / TanStack Query) caching/invalidation examples, and persistence patterns.

## Supported file types

Markdown (`.md`) for patterns/notes; small code snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`server-state.md`, `persisted-state.md`, `store-setup.md`).

## How agents should use it

Read when deciding where state belongs or wiring stores, alongside `../../../skills/mobile/mobile-state-management` and `mobile-server-state`. Keep server and client state separate.

## References are not requirements

Material here is **context, not commitment.** A state reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
