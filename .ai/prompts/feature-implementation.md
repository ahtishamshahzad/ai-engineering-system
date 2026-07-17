# Prompt: Feature Implementation

> Tool-neutral starter. Implement an approved feature.

---

Implement the approved **feature** (`.ai/projects/current/`, `.ai/templates/FEATURE.md`).

Preconditions (`.ai/hooks/before-feature.md`): Gate 4 approved; scope clear; only relevant skills loaded; if parallel, disjoint file ownership + shared contracts defined.

Implement within the assigned scope using the relevant domain skills (`.ai/skills/backend|web|mobile|database/…`). Enforce everything server-side; client checks are UX. Cover the required cases:
- happy path · invalid input · error path · **authorization denial** · regression.

Then: update docs/notes, keep `.ai/templates/PROGRESS.md` current, and run `.ai/hooks/after-feature.md`. Hand off for code review (+ security review if it touches auth/data/payments). Do not exceed the approved scope; route any cross-scope change through the orchestrator.
