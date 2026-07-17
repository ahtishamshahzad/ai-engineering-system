# `.ai/checklists/` — Reusable Review & Validation Checklists (Index)

Reusable checklists that make gate criteria and hook checks **concrete and repeatable** (`../system/QUALITY_GATES.md`, `../system/HOOK_RULES.md`). Each item is **observable and verifiable** — pass only when true — and each checklist states its fail/stop conditions.

## Index

### Planning
| Checklist | Verifies | Maps to |
|-----------|----------|---------|
| [`project-discovery.md`](project-discovery.md) | Requirements complete; audit-first. | Gate 1 / `before-discovery` |
| [`architecture.md`](architecture.md) | Apps/stack approved; boundaries + cross-cutting. | Gates 2–3 / `before-architecture` |

### Build (per surface)
| Checklist | Verifies |
|-----------|----------|
| [`mobile-foundation.md`](mobile-foundation.md) | Runtime decided; foundation; no bundle secrets; server enforces. |
| [`mobile-screen.md`](mobile-screen.md) | Design system, nav, states, a11y, tests. |
| [`web-page.md`](web-page.md) | Design, states, a11y, web-security, no bundle secrets. |
| [`dashboard-feature.md`](dashboard-feature.md) | Role + ownership/tenant gating; audit logging; negative tests. |
| [`api-endpoint.md`](api-endpoint.md) | Validation, authorization, error shape, limits, authz-denial tests. |
| [`database-change.md`](database-change.md) | Reviewed migration, constraints, indexes, backup/rollback. |
| [`authentication.md`](authentication.md) | Credential storage, lifecycle, enumeration/brute-force resistance. |
| [`authorization.md`](authorization.md) | Levels; in-query scope; untrusted IDs; negative tests. |

### Work Items & Quality
| Checklist | Verifies | Maps to |
|-----------|----------|---------|
| [`feature.md`](feature.md) | Scoped, required cases, acceptance met. | `before/after-feature` |
| [`bugfix.md`](bugfix.md) | Reproduce, root cause, failing-first regression. | `before-bugfix` |
| [`testing.md`](testing.md) | Selection, required cases, coverage-by-risk. | Gate 5 |
| [`code-review.md`](code-review.md) | Correctness, clarity, scope, AI-output modes. | Gate 6 |

### Delivery
| Checklist | Verifies | Maps to |
|-----------|----------|---------|
| [`commit.md`](commit.md) | Authorized, branch, no secrets, in scope. | `before-commit` |
| [`pull-request.md`](pull-request.md) | Gates 5–6, no secrets, integration-reviewed, authorized merge. | `before/after-pr` |
| [`deployment.md`](deployment.md) | Readiness, backups, rollback, approval, post-deploy smoke. | Gate 7 / `before/after-release` |
| [`release.md`](release.md) | Evidence-based Gate 7 go/no-go under approval. | Gate 7 / `before-release` |

## Conventions

- Each checklist maps to a gate or hook and states pass and fail/stop conditions.
- Items are observable and verifiable, not vague.
- Register each new checklist in this index.

## Related

Gates: `../system/QUALITY_GATES.md`. Hooks: `../hooks/`. Templates: `../templates/`.
