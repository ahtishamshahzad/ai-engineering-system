---
name: queues
description: Use to select and design queue infrastructure — broker choice (e.g. BullMQ/Redis, SQS, RabbitMQ), queue topology, workers/concurrency, delivery semantics, dead-letter queues, and monitoring. Job behavior itself is the background-jobs skill.
---

# Queues

## Purpose

Choose and design the queue transport that `background-jobs` runs on: broker, queue topology, worker model, delivery guarantees, dead-letter handling, and operational visibility.

## When to Use

- When background jobs need real infrastructure (beyond a single in-process timer).
- **Not** for defining job idempotency/retry behavior (`background-jobs`) or time triggers (`scheduled-jobs`).

## Inputs

- Job inventory (`background-jobs`): volume, latency tolerance, ordering needs, must-happen classification.
- Deployment shape and existing infrastructure (Redis present? cloud provider?).

## Discovery Questions

- Throughput and burst profile per job type? Latency tolerance (seconds vs hours)?
- Ordering requirements (per-entity ordering vs none)?
- What infrastructure already exists — is Redis/BullMQ, a cloud queue (SQS), or a broker (RabbitMQ) the low-operational-cost fit?
- Who watches the queues (dashboards, alerts)?

## Responsibilities

- Choose the broker on requirements + existing ops surface: BullMQ/Redis (Node-native, common default when Redis exists), managed cloud queues (low ops, at-least-once), RabbitMQ/Kafka-class only for routing/streaming needs that justify them — recorded with trade-offs (Gate 2 if it adds infrastructure).
- Design topology: queues per job class (not one global queue) so priorities/concurrency/rates tune independently; name conventions.
- Design workers: separate worker processes from the API (independent scaling/deploys), concurrency per queue, graceful shutdown (finish/return in-flight jobs on deploy).
- Set delivery semantics expectations: assume **at-least-once**; visibility timeouts/lock durations sized to job runtime; stalled-job recovery.
- Configure **dead-letter queues** per queue with alerting and a replay procedure (`backend-observability`).
- Plan backpressure: max queue depth alarms, producer behavior when saturated.

## Required Workflow

1. Derive throughput/ordering/latency needs from the job inventory.
2. Choose broker with trade-offs; record for approval if new infrastructure.
3. Define queue topology + per-queue concurrency/priority/rate.
4. Define worker deployment, shutdown, and stall recovery.
5. Configure DLQs, alerts, dashboards, replay runbook.
6. Specify tests: job survives worker crash, stalled job recovers, DLQ receives terminal failures.

## Decision Rules

- Don't add a broker class the team can't operate; managed/existing beats theoretically-better.
- One queue per job class; mixing critical and bulk work in one queue starves the critical.
- Visibility/lock timeout > worst-case job runtime, or jobs double-run mid-flight (`background-jobs` idempotency still required).
- Per-entity ordering needs are real design constraints (keyed queues/serialization) — don't fake with sleeps.

## Rules

- Queue infrastructure is a stack decision — user approval before adding it (`../../stack-recommendation`).
- Workers deploy and scale independently of the API.
- Every queue has a DLQ + alert; silent terminal loss is a defect.

## Anti-Patterns

- One global queue for everything.
- Workers inside the API process, dying with each deploy mid-job.
- Assuming exactly-once delivery.
- No DLQ — terminal failures retried forever or dropped.
- Unbounded queue growth with no alarms.

## Validation Checklist

- [ ] Broker chosen on requirements/ops fit; recorded (+ approval if new infra).
- [ ] Topology: queue per class, concurrency/priority per queue.
- [ ] Worker lifecycle: separate processes, graceful shutdown, stall recovery.
- [ ] At-least-once semantics acknowledged; timeouts sized.
- [ ] DLQs + alerts + replay runbook.
- [ ] Crash/stall/DLQ tests specified.

## Definition of Done

A recorded queue design — broker with trade-offs, per-class topology, worker lifecycle, at-least-once handling, DLQs with alerting and replay — ready to carry the job inventory.

## Related Skills

`background-jobs`, `scheduled-jobs`, `backend-observability`, `backend-deployment` (worker deploys), `../../stack-recommendation`, `nestjs-foundation` (Nest queue module when applicable).

## Related Knowledge

`../../../knowledge/` (existing infrastructure, ops capacity).

## Related References

`../../../references/backend/jobs/` (topology notes, when populated).

## Context Loading Guidance

- **Requires:** job inventory with volumes/ordering, infrastructure summary.
- **Does not require:** individual job logic, unrelated endpoints.
- **May load:** `background-jobs` (semantics alignment), `backend-observability`.
- **Stop when:** broker, topology, worker, and DLQ design are recorded.

## Token Efficiency Guidance

Decide from the job-inventory table; per-queue settings as a table. Link broker docs, don't restate them.
