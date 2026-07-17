# `.ai/references/` — External-Facing Reference Material (Index)

Reference material organized by **topic folder**. Agents read **only the relevant folder** for the active task (`../system/CONTEXT_MANAGEMENT_RULES.md`, `../system/TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Folders start empty and are populated as a project develops. **Do not create fake reference content.**

## Topic tree

```
references/
├── product/                  # briefs, research, personas, journeys
├── designs/                  # mockups, wireframes, design-system exports
├── mobile/                   # (index) screens · navigation · forms · state · native · notifications
├── web/                      # (index) pages · dashboard · forms · seo
├── backend/                  # (index) auth · authorization · api · database · errors
├── testing/                  # (index) playwright · maestro · api
└── deployment/               # targets, CI/CD & Docker patterns, runbooks, readiness (no secrets)
```

## Index

| Topic | Index / folder | Read when |
|-------|----------------|-----------|
| Product | [`product/`](product/README.md) | Analyzing requirements / shaping features. |
| Designs | [`designs/`](designs/README.md) | Building UI to a design. |
| Mobile | [`mobile/README.md`](mobile/README.md) | Building the mobile app (screens/nav/forms/state/native/notifications). |
| Web | [`web/README.md`](web/README.md) | Building web/dashboard (pages/dashboard/forms/seo). |
| Backend | [`backend/README.md`](backend/README.md) | Building the API/data layer (auth/authorization/api/database/errors). |
| Testing | [`testing/README.md`](testing/README.md) | Writing tests (playwright/maestro/api). |
| Deployment | [`deployment/`](deployment/README.md) | Setting up/running deploy & ops (no secrets). |

Every **terminal** folder has a README stating: what belongs there, supported file types, naming conventions, how agents should use it, and that **references do not automatically become requirements**.

## Conventions

- One topic per folder; documents focused and `kebab-case`.
- Long code examples belong here (or `../templates/`), **not** in permanent skills.
- Prefer linking to canonical `.ai/` content over restating it.
- **No secrets, credentials, or real production data** in any reference folder.

## References are not requirements (system-wide rule)

Reference material is **context, not commitment.** A file in `references/` does not become a requirement, an approved decision, or a task by virtue of existing here. Requirements live in `../templates/REQUIREMENTS.md` / the work item; decisions in `../templates/DECISION_RECORD.md`; approvals happen at the gates (`../system/QUALITY_GATES.md`). Agents cite references that inform a decision and never let an unreviewed reference silently drive scope.
