---
name: backend-performance
description: Use to diagnose and fix measured backend performance problems — profiling first, then N+1 and query work (with the database pack), caching with explicit invalidation, connection pooling, payload size, and event-loop health. No speculative optimization.
---

# Backend Performance

## Purpose

Improve backend performance the disciplined way: measure, find the actual bottleneck, apply the narrowest safe fix, and prove the improvement. Aligns with `../../performance-review`; database-level depth lives in `../../database/database-performance`.

## When to Use

- When latency/throughput/resource usage misses targets, with evidence.
- When setting performance budgets for critical endpoints.
- **Not** for speculative "make it faster" passes without measurement.

## Inputs

- Metrics/traces showing the symptom (`backend-observability`): which endpoints, which percentiles, since when.
- Performance targets per critical flow.

## Discovery Questions

- What exactly is slow — endpoint, percentile, load level? Since when (deploy? data growth?)?
- Where does the time go: database, external calls, serialization, event-loop blocking?
- What are the targets — and are they real requirements or vibes?

## Responsibilities

- **Measure first**: per-endpoint duration breakdowns, query logs, external-call timing; profile CPU/event-loop where compute-bound. The bottleneck is usually one of: N+1 queries, missing index, slow external call in the request path, oversized payloads, event-loop blocking.
- **Database work**: N+1 detection and batching/eager-loading, index needs, unbounded queries → delegate specifics to `../../database/database-performance` and `../../database/indexing`.
- **External calls**: move out of the request path (`background-jobs`), parallelize independent calls, cache stable responses (`third-party-integrations` timeouts already bound them).
- **Caching**: only for measured hot, stable data — with explicit **invalidation** and **stampede** stories; cache layers (in-process vs shared) chosen for the instance count. A cache without an invalidation design is a future correctness bug (`../../database/concurrency`).
- **Node specifics**: keep the event loop unblocked (no sync crypto/zlib/fs in handlers, chunk large JSON work); pool sizes matched to DB limits × instances.
- **Payloads**: pagination enforced, field selection over `SELECT *` dumps, compression at the edge.
- **Prove it**: re-measure under representative load; record before/after; watch for regressions via `backend-observability` dashboards.

## Required Workflow

1. Reproduce and quantify the symptom against the target.
2. Break down where time goes; identify the dominant contributor.
3. Apply the narrowest fix at that layer (delegate DB depth to the database pack).
4. Re-measure under representative conditions; compare to target.
5. Record fix + numbers; add a regression guard (alert threshold or test).

## Decision Rules

- No optimization without a measurement pointing at it.
- Fix the dominant contributor first; a 5ms micro-win under a 2s query is noise.
- Caching is the *last* resort after query/design fixes — it trades correctness risk for speed; record the invalidation path when taken.
- Scaling out is a valid fix only after per-instance efficiency is sane.

## Rules

- Before/after numbers accompany every performance change (unverified until run).
- Performance fixes must not weaken authorization/validation (no "skip the check, it's slow").
- Budgets for critical endpoints live with the endpoint design (`rest-api-design`).

## Anti-Patterns

- Adding Redis before finding the missing index.
- Optimizing code paths no measurement implicated.
- Caches with TTL-only "invalidation" for data users edit.
- `Promise.all` fan-outs that stampede the DB pool.
- Declaring victory from local benchmarks on empty tables.

## Validation Checklist

- [ ] Symptom quantified against a target.
- [ ] Time breakdown identifies the dominant contributor.
- [ ] Narrowest fix applied at the right layer.
- [ ] Cache additions include invalidation + stampede design.
- [ ] Before/after measured under representative load.
- [ ] Regression guard in place.

## Definition of Done

A measured bottleneck, a narrow fix at the responsible layer, proven improvement against the target under representative load, and a guard against regression — all recorded.

## Related Skills

`../../performance-review`, `../../database/database-performance`, `../../database/indexing`, `backend-observability`, `background-jobs`, `third-party-integrations`, `rest-api-design`.

## Related Knowledge

`../../../knowledge/` (targets, load profile, data growth).

## Related References

`../../../references/backend/performance/` (measurement notes, when populated).

## Context Loading Guidance

- **Requires:** the symptom with numbers, targets, relevant endpoint/query code.
- **Does not require:** the whole codebase, unrelated endpoints.
- **May load:** `../../database/database-performance` (query depth), `backend-observability`.
- **Stop when:** improvement is proven and recorded with a regression guard.

## Token Efficiency Guidance

Load only the implicated path. Report as symptom → cause → fix → numbers; skip the tour of everything examined.
