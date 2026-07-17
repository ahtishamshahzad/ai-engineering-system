# `.ai/prompts/` — Reusable Prompt Starters (Index)

Short, composable, **tool-neutral** starters for common operations. Paste one, fill the brackets, and run it. Prompts **compose with** the canonical rules (`../system/`) and reference skills/templates/hooks by path — they never duplicate or replace them.

## Index

### Lifecycle Start
| Prompt | Kicks off |
|--------|-----------|
| [`new-project.md`](new-project.md) | Full new-project pipeline (stops at Gates 2 & 4). |
| [`existing-project.md`](existing-project.md) | Audit-first enhancement of an existing repo. |
| [`continue-discovery.md`](continue-discovery.md) | Resume intake from recorded state with new answers. |
| [`approve-plan.md`](approve-plan.md) | Record a gate approval and proceed. |
| [`start-phase.md`](start-phase.md) | Begin an approved phase with pre-checks. |

### Build
| Prompt | Kicks off |
|--------|-----------|
| [`feature-plan.md`](feature-plan.md) | Plan a feature within architecture. |
| [`feature-implementation.md`](feature-implementation.md) | Implement an approved feature in scope. |
| [`bug-investigation.md`](bug-investigation.md) | Reproduce + root-cause before fixing. |
| [`bug-implementation.md`](bug-implementation.md) | Apply a fix with a failing-first regression test. |
| [`refactor-plan.md`](refactor-plan.md) | Plan a behavior-preserving refactor. |
| [`migration-plan.md`](migration-plan.md) | Plan an incremental, reversible migration. |

### Review & Audit
| Prompt | Kicks off |
|--------|-----------|
| [`code-review.md`](code-review.md) | Independent code review by severity. |
| [`security-audit.md`](security-audit.md) | Independent security assessment (Confirmed vs Potential). |
| [`performance-audit.md`](performance-audit.md) | Measure-first performance audit. |
| [`testing-audit.md`](testing-audit.md) | Coverage-by-risk assessment. |
| [`final-audit.md`](final-audit.md) | Aggregate reviews into a go/no-go. |

### Delivery
| Prompt | Kicks off |
|--------|-----------|
| [`github-setup.md`](github-setup.md) | CI from selected apps; remote actions approval-gated. |
| [`deployment.md`](deployment.md) | Approval-gated deploy with post-deploy smoke. |
| [`release.md`](release.md) | Gate 7 readiness and ship under approval. |

## Conventions

- Keep prompts short and composable; they point at `.ai/` rather than restating it.
- Every prompt preserves the gates and approval rules (no shipping/merging unasked).
- Register each new prompt in this index.

## Related

Templates they fill: `../templates/`. Workflows they follow: `../workflows/`. Rules: `../system/`.
