---
name: existing-backend-audit
description: Use to inventory an existing backend before changing it — framework, structure, API surface, auth/authorization model, data layer, jobs, integrations, tests, security posture. Produces findings that feed planning; changes nothing.
---

# Existing Backend Audit

## Purpose

Build an accurate picture of an existing backend — framework, layering, API surface, auth model, data access, async work, integrations, tests, and risks — before any change is planned. Read-only: it changes nothing.

## When to Use

- Before enhancing, refactoring, or migrating an existing backend.
- When onboarding this system onto a backend it hasn't seen.
- **Not** for greenfield projects (use `backend-stack-selection`).

## Inputs

- Repository access (read-only) and any prior audit in `../../../projects/current/` or `../../../generated/`.
- The request driving the audit (what change is coming).

## Discovery Questions

- What framework/runtime and versions are in use? Any deprecated majors?
- How is the code layered (routes/controllers/services/data)? Is business logic in handlers?
- How are authentication and authorization enforced, and where?
- What data layer and database are used? Migrations tracked?
- What background jobs, queues, webhooks, and third-party integrations exist?
- What is tested, and does CI run it?

## Responsibilities

- Inventory: framework, structure/layering, API surface (routes, versioning), validation approach, error handling, auth/authorization model, data layer + schema/migrations state, jobs/queues/schedulers, integrations/webhooks, config/secrets handling, logging/observability, tests + CI.
- Flag risks: missing authorization checks, unvalidated inputs, secrets in code, unbounded queries, missing rate limits on public endpoints, untested critical paths.
- Record findings where planning skills can consume them.

## Required Workflow

1. Map entry points and layering from the code (not from stale docs).
2. Trace one representative request end-to-end (route → validation → authz → service → data).
3. Inventory the areas above; note versions and drift.
4. List risks with evidence (file:line), separated **Confirmed** vs **Potential**.
5. Record findings for the planning skill that triggered the audit.

## Decision Rules

- Evidence over assumption: every claim cites a file or config.
- Depth follows the upcoming change — audit deeply where the change lands, broadly elsewhere.
- Security findings route to `../../security-review`; don't duplicate its assessment here.

## Rules

- Read-only. No fixes, no dependency changes, no "quick cleanups."
- Never print secrets found during the audit; flag location + rotation need only.
- Mark unverified behavior "unverified until run" rather than asserting it.

## Anti-Patterns

- Fixing issues mid-audit.
- Trusting README/docs over the actual code.
- Auditing every module at equal depth regardless of the planned change.
- Vague findings ("auth looks weak") without file references.

## Validation Checklist

- [ ] Framework, versions, layering mapped from code.
- [ ] One request traced end-to-end.
- [ ] API surface, auth model, data layer, async work, integrations inventoried.
- [ ] Tests + CI state recorded.
- [ ] Risks listed with evidence, Confirmed vs Potential.
- [ ] Nothing modified.

## Definition of Done

A recorded backend inventory + risk list with evidence, scoped to the upcoming change, consumable by planning skills — and an untouched codebase.

## Related Skills

`../../existing-project-audit`, `backend-stack-selection`, `backend-api-architecture`, `../../security-review`, `../../dependency-audit`, `../../environment-audit`, `../../database/database-selection`.

## Related Knowledge

`../../../knowledge/` (known architecture decisions, service history).

## Related References

`../../../references/backend/` (audit notes, when populated).

## Context Loading Guidance

- **Requires:** repo read access, the driving request.
- **Does not require:** frontend/mobile code, full git history, all references.
- **May load:** `../../security-review` (findings hand-off), `../../dependency-audit`.
- **Stop when:** findings are recorded for the planning skill.

## Token Efficiency Guidance

Sample representative files per layer instead of reading everything; trace one request deeply rather than all routes shallowly. Summarize findings — don't paste code bodies.
