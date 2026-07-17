---
name: incident-readiness
description: Use to prepare for production incidents — on-call and escalation, severity levels, runbooks for likely failures, alert-to-response wiring, communication paths, and blameless postmortems that feed regression tests. Preparation before incidents, not response during one.
---

# Incident Readiness

## Purpose

Be ready to respond to production incidents calmly and fast: known on-call and escalation, severity definitions, runbooks for the likely failures, and a learning loop that turns incidents into regression tests — set up **before** the incident, not improvised during it.

## When to Use

- Before production launch and as the system grows in criticality.
- **Not** for the deploy-reversal mechanism (`rollback-planning`) or metric/alert setup (`monitoring-logging`) — this wires those into human response.

## Inputs

- Monitoring/alerting (`monitoring-logging`), rollback plan (`rollback-planning`).
- Team/on-call structure, critical flows, business impact tolerances.

## Discovery Questions

- Who is on-call, how are they paged, and what's the escalation path if they don't respond?
- What are the severity levels, and what response/comms does each trigger?
- What are the most likely failures, and is there a runbook for each?
- How are incidents reviewed so the same one doesn't recur?

## Responsibilities

- **On-call + escalation**: defined rotation, paging path (from `monitoring-logging` alerts), and escalation when the first responder is unavailable — no "hope someone notices."
- **Severity levels**: clear definitions (e.g. SEV1 total outage/data loss → SEV3 minor degradation) each mapping to expected response time, who's involved, and comms.
- **Runbooks** for likely incidents (database down, deploy gone bad → `rollback-planning`, integration outage → `../../backend/third-party-integrations`, queue backlog, secret compromise → `../../security/secrets-audit`): symptoms, diagnosis steps, mitigation, and links to the relevant tools.
- **Alert-to-response wiring**: each critical alert leads to a runbook and an owner — closing the loop `monitoring-logging` opens.
- **Communication**: internal coordination channel + external status/comms path for user-facing incidents; who speaks for the incident.
- **Blameless postmortems**: after significant incidents, a factual timeline + root cause + action items — and **feed the fix into a regression test** (`../../testing/regression-testing`, `../../security/security-regression-testing`) so it can't silently recur; track action items to done.

## Required Workflow

1. Define on-call rotation + escalation + paging.
2. Define severity levels → response/comms per level.
3. Write runbooks for the likely failures; link tools.
4. Wire critical alerts → runbook + owner.
5. Set up incident comms (internal + external).
6. Establish blameless postmortems that produce regression tests + tracked actions.

## Decision Rules

- Preparation over improvisation — decisions made calm beforehand beat decisions made at 3 a.m. mid-outage.
- Every critical alert maps to a runbook and an owner, or it's noise.
- Postmortems are blameless and produce a regression test — an incident that leaves no test invites its own sequel.
- Severity drives response — not everything is a SEV1, and a real SEV1 shouldn't wait behind triage debate.

## Rules

- On-call, escalation, and severities are defined before launch.
- Likely failures have runbooks; critical alerts route to them.
- Significant incidents get blameless postmortems with tracked, test-backed actions.

## Anti-Patterns

- No on-call — incidents found by users tweeting.
- Alerts with no runbook, response improvised each time.
- Blameful postmortems that suppress honesty and learning.
- Postmortems with action items nobody tracks or tests.
- Everything treated as maximum severity (or nothing is).

## Validation Checklist

- [ ] On-call rotation + escalation + paging defined.
- [ ] Severity levels → response/comms mapped.
- [ ] Runbooks for likely failures; tools linked.
- [ ] Critical alerts wired to runbook + owner.
- [ ] Incident comms (internal + external) set.
- [ ] Blameless postmortem process feeding regression tests + tracked actions.

## Definition of Done

A recorded incident-readiness setup — on-call and escalation, severity levels, runbooks for likely failures wired from alerts, communication paths, and a blameless postmortem process that produces regression tests and tracked actions — all in place before production.

## Related Skills

`monitoring-logging`, `rollback-planning`, `production-readiness`, `../../backend/backend-observability`, `../../backend/third-party-integrations`, `../../security/secrets-audit`, `../../testing/regression-testing`, `../../security/security-regression-testing`, `../../release-planning`.

## Related Knowledge

`../../../knowledge/` (on-call structure, likely failures, impact tolerances).

## Related References

`../../../references/devops/` (runbooks, postmortem template, when populated).

## Context Loading Guidance

- **Requires:** monitoring/alerting, rollback plan, on-call structure, critical flows.
- **Does not require:** metric-emission internals, app feature code.
- **May load:** `rollback-planning`, `monitoring-logging`.
- **Stop when:** on-call, severities, runbooks, comms, and postmortem loop are recorded.

## Token Efficiency Guidance

The runbook set + severity table + on-call/escalation map are the artifacts; keep each runbook executable and short.
