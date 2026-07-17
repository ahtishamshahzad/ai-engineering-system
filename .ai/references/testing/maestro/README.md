# References — Testing / Maestro

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Maestro (mobile E2E / smoke) reference material: critical-flow scripts, smoke-flow definitions, element-based wait patterns, stable-selector conventions, and device/emulator CI notes.

## Supported file types

Markdown (`.md`) for patterns; Maestro flow snippets in fenced blocks.

## Naming conventions

`kebab-case` by flow (`login-flow.md`, `smoke-set.md`).

## How agents should use it

Read when writing mobile E2E/smoke tests, alongside `../../../skills/testing/maestro-e2e` and `../../../skills/mobile/mobile-maestro-e2e`. Reserve device E2E for critical flows; element-based waits, no sleeps.

## References are not requirements

Material here is **context, not commitment.** A testing reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
