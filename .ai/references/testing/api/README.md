# References — Testing / API

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

API integration test reference material: Supertest patterns, persona/auth-helper setups (anonymous / user A / user B / admin / other-tenant), the authorization-denial negative-test catalogue, and real-DB/isolation setup notes.

## Supported file types

Markdown (`.md`) for patterns; test snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`supertest-setup.md`, `authz-negative-suite.md`, `persona-helpers.md`).

## How agents should use it

Read when writing API integration tests, alongside `../../../skills/testing/api-integration-testing` and `../../../skills/backend/backend-integration-testing`. Use a production-engine DB; fake externals; cover the authorization-denial suite.

## References are not requirements

Material here is **context, not commitment.** A testing reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope.
