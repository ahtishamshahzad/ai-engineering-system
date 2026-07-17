# Prompt: Existing Project

> Tool-neutral starter. Audit before changing anything.

---

Work on an **existing project** through the AI Engineering System (`.ai/` canonical). Use `.ai/workflows/existing-project.md`.

**Request:** <what to enhance/fix/change>

Start by **auditing before proposing changes** (`.ai/skills/existing-project-audit`, plus `.ai/skills/backend/existing-backend-audit`, `dependency-audit`, `environment-audit` as relevant) → `.ai/templates/AUDIT.md`. Do not edit yet.

Then:
1. Analyze requirements for the change against the audit findings.
2. Choose the right sub-workflow (feature / refactor / migration) and plan scoped phases/tasks → `.ai/templates/PHASE_PLAN.md`, `TASK.md`.
3. If the change needs new applications or a stack change, **stop for Gate 2 approval**; otherwise proceed to Gate 4.

Respect existing architecture and conventions. Load only relevant skills. Report findings, assumptions, and blocking questions.
