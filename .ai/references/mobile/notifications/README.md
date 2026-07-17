# References — Mobile / Notifications

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Notification reference material: push/local notification setup, provider and permission notes, token handling, and tap→deep-link routing patterns.

## Supported file types

Markdown (`.md`) for patterns/notes; small code snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`push-setup.md`, `permissions.md`, `tap-to-deeplink.md`).

## How agents should use it

Read when implementing notifications, alongside `../../../skills/mobile/mobile-notifications` and `mobile-deep-linking`.

## References are not requirements

Material here is **context, not commitment.** A notifications reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
