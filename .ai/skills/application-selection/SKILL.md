---
name: application-selection
description: Use to decide which applications a project actually needs (mobile, public web, admin dashboard, marketing site, backend, worker, real-time service, database, file storage, shared packages). Each is evaluated independently with justification; nothing is auto-scaffolded.
---

# Application Selection

## Purpose

Decide, with justification, which applications a project needs — evaluating each candidate independently rather than scaffolding everything. Implements `../../system/APPLICATION_SELECTION_RULES.md`.

## When to Use

- After requirements are baselined, before stack recommendation.
- When a project could plausibly need multiple applications and the set must be decided.
- **Not** when the application set is already approved and unchanged.

## Inputs

- Requirement baseline (`requirements-analysis`).
- Audit findings for existing projects (`existing-project-audit`).

## Discovery Questions

- Who are the distinct audiences (end users, operators, marketing visitors)?
- Is there a real-time or background-processing need yet?
- Is persistent storage or file/media storage required?
- Are there ≥2 real consumers that justify a shared package?

## Responsibilities

- Evaluate each candidate: mobile app · public customer web app · admin dashboard · marketing site · backend API · background worker · real-time service · database · file storage · shared packages.
- For each: is the need real? can an existing app absorb it? distinct audience/lifecycle? cost now vs deferring?
- Output three lists: **Selected** (with justification), **Deferred** (with trigger), **Excluded** (with reason).
- Keep public web app, admin dashboard, and marketing site as **separate** decisions.

## Required Workflow

1. Read the requirement baseline (+ audit).
2. Walk each candidate through the four questions.
3. Select only where the need is real and not better absorbed.
4. Record Selected / Deferred / Excluded.
5. Hand off to `stack-recommendation` (for selected apps only).

## Decision Rules

- Public web app ≠ admin dashboard ≠ marketing site — evaluate separately.
- Backend API ≠ database — both may be needed; they are separate selections.
- "Might need later" → **Deferred** with a trigger, not built now.
- No real-time service or worker until a feature requires async/live behavior.
- No shared package until ≥2 real consumers exist.

## Rules

- Do not auto-scaffold every candidate.
- Every selected app has a one-line justification.
- Deferred candidates name the trigger that would justify them.

## Anti-Patterns

- "It's full-stack, so build mobile + web + dashboard + backend + DB."
- Merging marketing site into the product app by default.
- Adding infrastructure (worker/real-time) speculatively.

## Validation Checklist

- [ ] Every candidate evaluated.
- [ ] Selected apps justified.
- [ ] Deferred candidates have triggers.
- [ ] Excluded candidates have reasons.
- [ ] Web app / dashboard / marketing kept separate.

## Definition of Done

A recorded Selected/Deferred/Excluded application set with justifications, ready for `stack-recommendation` and Gate 2.

## Related Skills

`requirements-analysis`, `existing-project-audit`, `stack-recommendation`, `architecture-design`, `project-orchestrator`.

## Related Knowledge

`../../knowledge/` (domain constraints affecting audiences).

## Related References

None required.

## Context Loading Guidance

- **Requires:** requirement baseline, audit findings (if any).
- **Does not require:** the stack decision space, references, or other skills' bodies.
- **May load:** none (returns to orchestrator/stack-recommendation).
- **Stop when:** Selected/Deferred/Excluded is recorded.

## Token Efficiency Guidance

Reason from the requirement summary, not raw source. Keep the three lists terse — justification is one line each.
