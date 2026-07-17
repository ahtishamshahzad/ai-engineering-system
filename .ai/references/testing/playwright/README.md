# References — Testing / Playwright

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Playwright (web E2E) reference material: critical-journey scripts, resilient-locator patterns, auto-wait usage, fixture/seed patterns, and CI-integration notes.

## Supported file types

Markdown (`.md`) for patterns; small test snippets in fenced blocks.

## Naming conventions

`kebab-case` by journey/concern (`checkout-journey.md`, `locator-patterns.md`).

## How agents should use it

Read when writing web E2E tests, alongside `../../../skills/testing/playwright-e2e`. Reserve E2E for critical journeys; no fixed sleeps.

## References are not requirements

Material here is **context, not commitment.** A testing reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
