# References — Backend / API

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

API reference material: endpoint conventions, the API contract (or a link to the OpenAPI/GraphQL schema source of truth), pagination/versioning/idempotency patterns, response-envelope conventions, and GraphQL schema/resolver notes.

## Supported file types

Markdown (`.md`) for conventions; the OpenAPI/GraphQL schema file itself may live here or be linked; small snippets in fenced blocks.

## Naming conventions

`kebab-case` by concern (`endpoint-conventions.md`, `pagination.md`, `versioning.md`); schema as `openapi.yaml` / `schema.graphql` where kept here.

## How agents should use it

Read when designing/consuming the API, alongside `../../../skills/backend/rest-api-design`, `graphql-api-design`, and `api-contracts`. The contract is the single source of truth.

## References are not requirements

Material here is **context, not commitment** — except a designated contract source of truth, which *is* authoritative for shape once approved. Other notes do not automatically become requirements; requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`).
