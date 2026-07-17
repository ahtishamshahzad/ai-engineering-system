---
name: backend-observability
description: Use to plan backend observability — structured PII-safe logging with request correlation, metrics (rate/errors/duration, queues, jobs), health checks, alerting on symptoms, and tracing when the topology earns it.
---

# Backend Observability

## Purpose

Make the backend diagnosable and its failures visible: structured logs you can query, metrics that answer "is it healthy," alerts that fire on user-facing symptoms, and correlation from request to log line.

## When to Use

- When establishing a backend foundation, before incidents — not after the first one.
- **Not** for client-side analytics or product metrics.

## Inputs

- Deployment/runtime shape (where logs/metrics can go); async surfaces (`queues`, `scheduled-jobs`).
- Critical user flows (what must alert when broken).

## Discovery Questions

- Where do logs and metrics land (platform-provided vs chosen stack — a vendor decision)?
- Which flows are business-critical enough to page on?
- What volume/cost constraints bound log verbosity?

## Responsibilities

- **Structured logging**: JSON logs with level, timestamp, **request/correlation ID** (generated at ingress, propagated through services, jobs, and outbound calls), route, actor ID (not PII), duration, outcome. Levels used consistently; debug off in production by config.
- **PII/secret safety**: denylist-by-design — no tokens, passwords, emails-in-clear, payload dumps; redaction at the logger, not by discipline (`backend-security` audit events coordinate here).
- **Metrics**: RED per endpoint class (rate, error %, duration percentiles), plus queue depth/job failures (`queues`), scheduled-job heartbeats (`scheduled-jobs`), external-call latency/error per provider (`third-party-integrations`), DB pool/query health (`../../database/database-performance`).
- **Health checks**: liveness (process up) vs readiness (dependencies reachable) — separate endpoints, used by the deploy target (`backend-deployment`).
- **Alerting on symptoms**: error-rate and latency thresholds on critical flows, DLQ growth, missed schedule heartbeats — every alert actionable, with a route to logs via the correlation ID.
- **Tracing**: adopt distributed tracing when multiple services/queues make log-hopping painful — a justified addition, not a default.

## Required Workflow

1. Choose log/metric destinations (approval if new vendor/infra).
2. Define the log schema + correlation-ID propagation (HTTP → jobs → outbound).
3. Define the metric set per surface (endpoints, queues, schedules, providers, DB).
4. Define liveness/readiness checks.
5. Define alerts for critical symptoms, each with an owner and a runbook line.
6. Verify redaction with a test (secret-shaped values never reach the sink).

## Decision Rules

- Log events, not narration: one entry per request/job outcome beats step-by-step spam.
- Alert on user-visible symptoms (error rate, latency, backlog), not causes (CPU) — causes go on dashboards.
- Correlation ID crosses every async boundary or debugging stops at the queue.
- Sampling/retention are cost decisions — record them; don't discover them on the invoice.

## Rules

- No PII/secrets in logs — enforced by redaction code, verified by test.
- Every alert has an owner and an action; unactionable alerts get deleted, not muted.
- Health checks are cheap and dependency-honest (readiness fails when the DB is gone).

## Anti-Patterns

- `console.log` prose scattered through services.
- Logging request/response bodies wholesale.
- One `/health` returning 200 unconditionally.
- Alerts on every 5xx blip → pager fatigue → ignored pages.
- Request ID that dies at the queue boundary.

## Validation Checklist

- [ ] Log schema + levels + correlation propagation defined.
- [ ] Redaction wired and tested.
- [ ] Metrics per surface (RED, queues, schedules, providers, DB).
- [ ] Liveness/readiness split and wired to deploys.
- [ ] Symptom alerts with owners/runbooks.
- [ ] Tracing decision recorded (adopted or explicitly deferred).

## Definition of Done

A recorded observability design — structured correlated PII-safe logs, per-surface metrics, honest health checks, actionable symptom alerts, and an explicit tracing decision — wired into deploy and job surfaces.

## Related Skills

`backend-error-handling`, `backend-performance`, `backend-security`, `queues`, `scheduled-jobs`, `third-party-integrations`, `backend-deployment`, `../../performance-review`.

## Related Knowledge

`../../../knowledge/` (critical flows, cost constraints).

## Related References

`../../../references/backend/observability/` (schema/alert tables, when populated).

## Context Loading Guidance

- **Requires:** runtime shape, critical-flow list, async surface inventory.
- **Does not require:** vendor documentation, dashboard cosmetics.
- **May load:** `backend-error-handling` (log points), `queues`/`scheduled-jobs` (metrics).
- **Stop when:** schema, metrics, checks, and alerts are recorded.

## Token Efficiency Guidance

Three tables carry the design: log schema fields, metric list per surface, alert list (symptom → threshold → owner).
