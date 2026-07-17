# Prompt: Refactor Plan

> Tool-neutral starter. Plan a behavior-preserving refactor.

---

Plan a **refactor** (`.ai/` canonical; `.ai/workflows/refactor.md`, `.ai/skills/refactor-planning`).

**Target:** <area to improve> · **Goal:** <structure/clarity — not new behavior>

Do this:
1. State the exact behavior to **preserve**.
2. Confirm a **test safety net** exists for it; if not, add characterization tests **first** (`.ai/skills/testing/test-coverage-audit`).
3. Plan **small, reversible, independently verifiable steps** → `.ai/templates/REFACTOR.md`.
4. If the change would alter behavior, **stop and reclassify** as a feature or bugfix.

Run `.ai/hooks/before-refactor.md`. Present the plan before changing code.
