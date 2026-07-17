# References — Product

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Product context: briefs, user research, personas, journey notes, competitor/market notes, feature rationale, and prioritization notes — the *why* behind the product.

## Supported file types

Markdown (`.md`) for notes; PDFs or images for supplied research artifacts (linked/embedded, not pasted as walls of text).

## Naming conventions

`kebab-case.md`, prefixed by theme where useful (`persona-admin.md`, `journey-checkout.md`). Date research notes in-file (`YYYY-MM-DD`).

## How agents should use it

Read when analyzing requirements or shaping features, to ground decisions in real product context. Cite the relevant file in `REQUIREMENTS.md` / `FEATURE.md` when it informs scope.

## References are not requirements

Material here is **context, not commitment.** A file here does **not** automatically become a requirement, an approved decision, or a task. Requirements live in `../../templates/REQUIREMENTS.md` / the work item; decisions in `../../templates/DECISION_RECORD.md`; approvals happen at the gates (`../../system/QUALITY_GATES.md`). Treat references as inputs to consider, cite them when they inform a decision, and never let an unreviewed reference silently drive scope.
