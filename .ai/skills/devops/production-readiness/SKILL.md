---
name: production-readiness
description: Use as the pre-launch go/no-go checklist — confirming tests/security passed, config/secrets/migrations/backups/monitoring/rollback/incident-response are all in place before a production deploy. Aggregates readiness signals; the release gate. Approval required to ship.
---

# Production Readiness

## Purpose

Answer one question before shipping: **is this safe to put in front of users?** A go/no-go aggregation of the readiness signals — tests, security, config, data safety, observability, rollback, incident response — so nothing critical is discovered in production. Aligns with `../../release-planning` (Gate 7).

## When to Use

- Before any first or significant production deploy.
- **Not** as a substitute for the individual skills — it confirms their outputs exist and pass.

## Inputs

- Outputs of the testing, security, and devops skills for this release.
- The deployment plan (`../../backend/backend-deployment`), rollback plan (`rollback-planning`), incident plan (`incident-readiness`).

## Discovery Questions

- Have the required tests and security checks passed on the release artifact?
- Are config/secrets, migrations, backups, monitoring, rollback, and incident response actually ready — not just planned?
- What are the explicit go/no-go criteria, and who approves the ship?

## Responsibilities

- Confirm each readiness area — treating "planned" and "verified" as different:
  - **Quality**: required tests green (`../../testing/`), risk-based coverage adequate (`../../testing/test-coverage-audit`), critical E2E + smoke pass; no known release-blocking bugs.
  - **Security**: security review done (`../../security-review`, `../../security/`), no unresolved high findings, dependency + secrets scans clean (`../../security/dependency-security`, `secrets-audit`).
  - **Config/secrets**: env validated per environment, secrets in the store, nothing in bundles (`environment-management`, `secrets-management`).
  - **Data**: migrations rehearsed on staging, **backups + tested restore** in place (`../../database/database-migrations`, `../../database/backup-recovery`).
  - **Observability**: logging/metrics/health checks/alerts live (`monitoring-logging`, `../../backend/backend-observability`).
  - **Rollback**: a rehearsed rollback path exists (`rollback-planning`).
  - **Incident**: on-call, runbooks, escalation ready (`incident-readiness`).
- Produce a **go/no-go**: pass with evidence, or a blocking list. **Shipping is approval-gated** (`../../release-planning`, `../../git-workflow`).

## Required Workflow

1. Enumerate readiness areas for this release.
2. Verify each against evidence (green runs, live alerts, tested restore) — not assertions.
3. List blockers; classify must-fix vs acceptable-with-note.
4. Produce the go/no-go with evidence.
5. Route to approval before deploy.

## Decision Rules

- "Planned" is not "ready" — verify the restore ran, the alert fired, the rollback was rehearsed.
- Any unresolved high-severity security or data-safety item is a no-go.
- Missing rollback or incident response is a no-go for a significant deploy — you're flying without a net.
- Go/no-go is evidence-based; green vibes don't ship.

## Rules

- Readiness is verified, not assumed.
- The deploy is approval-gated regardless of a green checklist.
- Blockers are explicit and owned.

## Anti-Patterns

- Checking boxes for things merely planned, not verified.
- Shipping with unresolved high security findings "to be fixed after."
- No tested restore, no rehearsed rollback, no on-call.
- Treating the checklist as a formality and deploying on optimism.
- Skipping post-deploy smoke (`../../testing/smoke-testing`).

## Validation Checklist

- [ ] Tests green; risk-based coverage adequate; smoke/E2E pass.
- [ ] Security review + scans clean; no unresolved high findings.
- [ ] Config validated; secrets in store; none in bundles.
- [ ] Migrations rehearsed; backups + tested restore in place.
- [ ] Monitoring/alerts live; rollback rehearsed; incident response ready.
- [ ] Evidence-based go/no-go produced; approval routed.

## Definition of Done

An evidence-based go/no-go for the release — every readiness area verified (not just planned), blockers explicit and owned — routed to approval, with rollback, backups, monitoring, and incident response confirmed ready before any production deploy.

## Related Skills

`../../release-planning`, `../../final-quality-audit`, `rollback-planning`, `incident-readiness`, `monitoring-logging`, `../../backend/backend-deployment`, `../../database/backup-recovery`, `../../database/database-migrations`, `environment-management`, `secrets-management`, `../../security-review`, `../../testing/smoke-testing`.

## Related Knowledge

`../../../knowledge/` (release scope, go/no-go criteria).

## Related References

`../../../references/devops/` (readiness checklists, when populated).

## Context Loading Guidance

- **Requires:** the release's test/security/devops outputs, deploy/rollback/incident plans.
- **Does not require:** re-running each skill (confirm their evidence), app feature code.
- **May load:** `rollback-planning`, `incident-readiness`, `../../release-planning`.
- **Stop when:** the evidence-based go/no-go is produced and routed to approval.

## Token Efficiency Guidance

The readiness checklist with an evidence link per line is the artifact; verify signals, don't re-derive them.
