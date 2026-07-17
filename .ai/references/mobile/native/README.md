# References — Mobile / Native

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Native-capability reference material: notes on native modules and config plugins, camera/media, location/maps, background tasks, permissions handling, and existing-native-project integration details.

## Supported file types

Markdown (`.md`) for notes; small code/config snippets in fenced blocks; links to SDK docs rather than pasted docs.

## Naming conventions

`kebab-case` by capability (`camera.md`, `background-tasks.md`, `permissions.md`).

## How agents should use it

Read when adding a native capability, alongside `../../../skills/mobile/mobile-native-modules` and the specific capability skill. Evaluate packages/config plugins first; custom modules only when needed.

## References are not requirements

Material here is **context, not commitment.** A native reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
