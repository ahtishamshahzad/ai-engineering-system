# References — Deployment

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Deployment/operations reference material: deployment-target notes, CI/CD pipeline and workflow patterns, Dockerfile patterns, environment/config templates (**no secret values**), rollback runbooks, monitoring/alert catalogues, and readiness/postmortem records. Config templates list variables and purposes, never real secrets.

## Supported file types

Markdown (`.md`) for runbooks/notes; workflow/Dockerfile/config **templates** in fenced blocks or template files; **no `.env` with real values.**

## Naming conventions

`kebab-case` by concern (`rollback-runbook.md`, `ci-pipeline.md`, `env-template.md`, `readiness-checklist.md`).

## How agents should use it

Read when setting up or running deployment/ops, alongside `../../skills/devops/README.md`. Deploys are approval-gated; CI is generated from the applications that exist. **Secrets live in the secret store, never here.**

## References are not requirements

Material here is **context, not commitment.** A deployment reference does **not** automatically become a requirement or an approval to deploy. Requirements/tasks and gate approval govern the work (`../../system/QUALITY_GATES.md`); remote/publish/deploy actions require explicit user approval. Cite when it informs the work; never let it silently drive scope. **Never store secrets here.**
