---
name: scheduled-jobs
description: Use to plan time-triggered work — cron schedules, single-execution across multiple instances (distributed locks/leader), missed-run and overlap policy, timezone/DST handling, and monitoring that detects jobs that silently stop running.
---

# Scheduled Jobs

## Purpose

Design work that runs on a schedule (cleanups, digests, syncs, report generation) so it runs **once per tick across all instances**, handles missed and overlapping runs deliberately, and can't silently stop.

## When to Use

- For cron-style, time-triggered work.
- **Not** for request-triggered async work (`background-jobs`) or transport choice (`queues`).

## Inputs

- Scheduled-work inventory: what, how often, criticality of each tick.
- Deployment shape (instance count — determines the single-execution mechanism).

## Discovery Questions

- Per job: what breaks if a tick is skipped? If it runs twice? If two runs overlap?
- Multiple instances — where does "exactly one runner" come from (queue scheduler, distributed lock, leader, external cron hitting one target)?
- Timezone semantics: UTC ticks or user-local (DST-sensitive) schedules?

## Responsibilities

- Inventory scheduled work with frequency, runtime estimate, criticality, and tick semantics (skip-ok / must-catch-up / must-not-double).
- Choose the scheduler: in-process cron only for single-instance; multi-instance uses queue-based repeatable jobs (`queues`), a distributed lock, or an external scheduler triggering an endpoint/job — one recorded mechanism, not "each instance runs it and we hope."
- Define **overlap policy** per job: skip tick if previous still running, or queue behind — long-running jobs must not stack.
- Define **missed-run policy**: catch up (with bounded backlog) vs skip; deploys/restarts around tick time are the common trigger.
- Make handlers idempotent and chunked (a tick that processes "everything" must survive re-run and partial completion — `background-jobs` rules apply).
- Handle timezones explicitly: store schedules in UTC; user-local schedules recompute across DST.
- Monitor **absence**: every scheduled job emits success heartbeats; alert when a tick doesn't happen (`backend-observability`) — a dead scheduler is silent by nature.

## Required Workflow

1. Build the schedule inventory with tick semantics.
2. Choose the single-execution mechanism for the deployment shape.
3. Set overlap + missed-run policy per job.
4. Design handlers as idempotent, chunked, scoped (`ownership-authorization` for tenant data).
5. Wire success-heartbeat monitoring + failure alerts.
6. Specify tests: double-trigger runs once (lock works), overlap policy holds, missed-tick alert fires.

## Decision Rules

- Multi-instance + in-process cron without a lock = N executions; never ship it.
- Prefer the queue's repeatable-job facility when a queue exists — one mechanism, DLQs and retries included.
- Business-critical ticks (billing, digests) get must-catch-up semantics and explicit backlog bounds; cleanups usually skip.
- Long jobs become a scheduled *enqueuer* of chunked background jobs, not one giant tick.

## Rules

- Every scheduled job has a recorded owner, schedule, overlap policy, and missed-run policy.
- Handlers are idempotent — schedulers double-fire around restarts.
- No schedule ships without absence monitoring.

## Anti-Patterns

- `setInterval`/node-cron on every instance of a scaled service.
- Ticks that assume the previous tick finished.
- Catch-up logic that replays an unbounded backlog at once.
- Local-time cron expressions surprising everyone at DST.
- Scheduled jobs discovered dead weeks later ("the digest hasn't sent since the deploy").

## Validation Checklist

- [ ] Inventory with frequency, runtime, criticality, tick semantics.
- [ ] Single-execution mechanism fits instance count.
- [ ] Overlap + missed-run policy per job.
- [ ] Handlers idempotent, chunked, scoped.
- [ ] Heartbeat/absence alerts wired.
- [ ] Double-trigger and overlap tests specified.

## Definition of Done

A recorded schedule inventory with a single-execution mechanism, per-job overlap/missed-run policies, idempotent chunked handlers, and absence monitoring — tested against double-trigger and overlap.

## Related Skills

`background-jobs`, `queues`, `backend-observability`, `../../database/concurrency` (locks), `backend-deployment` (restart interaction).

## Related Knowledge

`../../../knowledge/` (business schedules, criticality).

## Related References

`../../../references/backend/jobs/` (schedule inventory, when populated).

## Context Loading Guidance

- **Requires:** scheduled-work inventory, instance count.
- **Does not require:** request-path design, broker internals beyond the scheduling facility.
- **May load:** `queues` (repeatable jobs), `backend-observability` (absence alerts).
- **Stop when:** inventory, mechanism, policies, and monitoring are recorded.

## Token Efficiency Guidance

The schedule table (job, cron, semantics, policies, alert) is the deliverable — one row per job.
