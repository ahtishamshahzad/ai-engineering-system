# References — Backend / Auth

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Authentication (identity) reference material: session/JWT lifecycle notes, credential-hashing choices, token refresh/rotation/revocation patterns, and account-flow diagrams (signup/verify/reset/OTP/MFA). **No real secrets or credentials.**

## Supported file types

Markdown (`.md`) for patterns/notes; small code snippets in fenced blocks; flow diagrams.

## Naming conventions

`kebab-case` by concern (`jwt-lifecycle.md`, `password-reset-flow.md`, `session-vs-jwt.md`).

## How agents should use it

Read when implementing/reviewing authentication, alongside `../../../skills/backend/backend-authentication` and `../../../skills/security/authentication-security`. Authentication ≠ authorization.

## References are not requirements

Material here is **context, not commitment.** An auth reference does **not** automatically become a requirement. Requirements/tasks and gate approval govern the build (`../../../system/QUALITY_GATES.md`). Cite when it informs the work; never let it silently drive scope. **Never store secrets here.**
