---
name: monitoring-logging
description: Use to plan production monitoring and logging at the ops level — centralized structured logs, key metrics/dashboards, health checks, symptom-based alerting with ownership, uptime/synthetic checks, and cost/retention — so failures are seen fast. Application instrumentation is backend-observability.
---

# Monitoring & Logging

## Purpose

Ensure production failures are **seen quickly and diagnosed fast**: logs centralized and searchable, the right metrics on dashboards, health/uptime checks, and alerts that page on user-facing symptoms with clear ownership. The application-code instrumentation lives in `../../backend/backend-observability`; this skill is the ops/platform view.

## When to Use

- When establishing production monitoring for the deployed system.
- **Not** for in-code log/metric emission design (`../../backend/backend-observability`) or incident *response* (`incident-readiness`).

## Inputs

- Instrumentation emitted by the apps (`../../backend/backend-observability`) and health endpoints.
- Deployment targets (`deployment-selection`), critical flows, on-call structure.

## Discovery Questions

- Where do logs and metrics aggregate (platform-provided vs a chosen stack — a vendor decision), and are they searchable across services?
- Which user-facing symptoms warrant paging (error rate, latency, downtime, queue backlog)?
- Who owns each alert, and what's the retention/cost budget?

## Responsibilities

- **Centralize logs**: structured logs from all services/instances shipped to one searchable place, correlatable by request ID (`../../backend/backend-observability`); retention + sampling set to cost (`incident-readiness` needs history).
- **Metrics + dashboards**: the golden signals (error rate, latency, throughput, saturation) per service, plus queue depth/job failures/scheduled-job heartbeats (`../../backend/queues`, `../../backend/scheduled-jobs`) and infra (CPU/mem/DB pool — `../../database/database-performance`).
- **Health + uptime checks**: liveness/readiness wired to the platform; external synthetic/uptime checks on critical endpoints (catches total-down that internal metrics can't report).
- **Symptom-based alerting**: page on user-visible symptoms, not raw causes; every alert **actionable, owned, and routed** to on-call (`incident-readiness`) with a runbook link — tune to avoid pager fatigue.
- **Verify redaction** holds at the aggregation layer: no PII/secrets in shipped logs (`../../security/privacy-review`, `secrets-management`).

## Required Workflow

1. Confirm apps emit structured, correlated logs + metrics.
2. Centralize logs; set retention/sampling to budget.
3. Build dashboards for golden signals + queues/jobs/infra.
4. Wire health + external uptime checks.
5. Define owned, actionable, symptom-based alerts with runbook links.
6. Verify no PII/secrets reach the log store.

## Decision Rules

- Alert on symptoms (users hurting), dashboard the causes — paging on CPU spikes breeds fatigue.
- External uptime checks are non-negotiable: internal metrics can't report "the whole thing is down."
- Every alert has an owner and an action, or it's deleted — unactionable alerts get muted, then everything gets ignored.
- Retention long enough to investigate incidents; sampling to control cost — a recorded trade-off.

## Rules

- Logs centralized, searchable, correlated, PII/secret-free.
- Alerts owned, actionable, symptom-based.
- Uptime/synthetic checks on critical paths.

## Anti-Patterns

- Logs scattered per-instance, unsearchable.
- Paging on every 5xx blip or CPU tick → pager fatigue → ignored pages.
- No external uptime check (blind to full outages).
- Alerts with no owner or no runbook.
- PII/secrets leaking into the aggregated logs.

## Validation Checklist

- [ ] Centralized, searchable, correlated logs; retention/sampling set.
- [ ] Dashboards: golden signals + queues/jobs/infra.
- [ ] Health + external uptime checks wired.
- [ ] Symptom-based, owned, actionable alerts with runbooks.
- [ ] No PII/secrets in the log store.

## Definition of Done

Centralized searchable logs, dashboards for the golden signals and async/infra surfaces, health + external uptime checks, and owned symptom-based alerts with runbooks — tuned against fatigue and verified PII/secret-free — so production failures are seen fast.

## Related Skills

`../../backend/backend-observability`, `incident-readiness`, `rollback-planning`, `production-readiness`, `deployment-selection`, `../../backend/queues`, `../../backend/scheduled-jobs`, `../../database/database-performance`, `../../security/privacy-review`.

## Related Knowledge

`../../../knowledge/` (critical flows, on-call, cost budget).

## Related References

`../../../references/devops/` (dashboard/alert catalogs, when populated).

## Context Loading Guidance

- **Requires:** app instrumentation, health endpoints, critical flows, on-call structure.
- **Does not require:** in-code emission design, app feature code.
- **May load:** `incident-readiness`, `../../backend/backend-observability`.
- **Stop when:** centralization, dashboards, checks, and alerts are defined.

## Token Efficiency Guidance

Three artifacts: dashboard signal list, alert table (symptom→threshold→owner→runbook), and the retention/cost note.
