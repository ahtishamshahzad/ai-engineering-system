# References — Backend (Topic Index)

> Backend reference material, organized by sub-topic. Agents read **only the relevant sub-folder** for the active task (`../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Each sub-folder starts empty; material is added as the project develops. **Do not create fake reference content.** **Never store secrets or real data here.**

## Sub-topics

| Folder | Covers |
|--------|--------|
| [`auth/`](auth/README.md) | Authentication (identity): session/JWT lifecycle, account flows. |
| [`authorization/`](authorization/README.md) | Authorization matrix, roles/permissions, ownership/tenant scoping, negative tests. |
| [`api/`](api/README.md) | Endpoint conventions, the contract (OpenAPI/GraphQL), pagination/versioning. |
| [`database/`](database/README.md) | Schema/ER notes, data-layer patterns, migration playbooks, index analyses. |
| [`errors/`](errors/README.md) | Error taxonomy, safe response shape, status mapping. |

## Conventions

Each terminal sub-folder's README states what belongs there, file types, naming, how agents use it, and that **references do not automatically become requirements**. Pairs with the backend skills (`../../skills/backend/README.md`).
