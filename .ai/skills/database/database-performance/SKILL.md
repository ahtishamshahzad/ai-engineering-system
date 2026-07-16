---
name: database-performance
description: Use to diagnose and fix measured database performance problems — slow-query identification, EXPLAIN analysis, N+1 elimination, pagination of unbounded reads, connection-pool sizing, and lock-contention diagnosis. Evidence first; indexes via the indexing skill.
---

# Database Performance

## Purpose

Find and fix what's actually slow at the database layer: identify the guilty queries with evidence, read their plans, fix shape/index/pooling — and prove the improvement. Pairs with `../backend/backend-performance` (which owns the app layer) and delegates index design to `indexing`.

## When to Use

- When metrics/slow-query logs show database time hurting endpoints or jobs.
- Before launch, to validate hot paths at realistic volume.
- **Not** for speculative tuning with no measurement.

## Inputs

- Evidence: slow-query logs, per-endpoint DB timing (`../backend/backend-observability`), pool metrics.
- Schema + index state, data volumes; the implicated query code (data layer).

## Discovery Questions

- Which queries dominate — by total time (frequency × duration), not just worst single case?
- Is the data volume representative (dev's 100 rows vs prod's 10M)?
- Is it the query, the *number* of queries (N+1), the pool, or locks?

## Responsibilities

- **Identify** with evidence: slow-query log / `pg_stat_statements`-class stats / Mongo profiler; rank by aggregate cost.
- **Read plans** (EXPLAIN ANALYZE / `.explain()`): seq scans on big tables, misestimated rows, sort spills, index misses → shape or index fixes (`indexing` owns index design).
- **Kill N+1s**: per-item queries from ORM lazy relations (`prisma-relational` include shaping, `drizzle-relational` joins, `mongoose-mongodb` populate policy) — batch, join, or restructure; verify by counting queries per request.
- **Bound reads**: pagination on every list (`../backend/rest-api-design`), projections over full rows, streaming/chunking for exports (`data-migration`-style batching for bulk work).
- **Size the pool**: connections = f(instances × pool) vs database limits; diagnose exhaustion vs saturation (waiting-for-connection vs slow-query symptoms differ); transactions holding connections during slow work → `transactions` eviction rules.
- **Diagnose locks**: blocked-query analysis, long-transaction offenders, hot-row contention → `concurrency`/`transactions` fixes.
- **Prove it**: before/after timing at representative volume; regression guards (slow-query alerting thresholds — `../backend/backend-observability`).
- Caching enters only after query fixes, with invalidation design (`../backend/backend-performance` owns the cache decision).

## Required Workflow

1. Rank offenders by aggregate cost from real measurements.
2. Classify each: bad plan / N+1 / unbounded read / pool / locks.
3. Fix at the right layer (query shape here, indexes via `indexing`, boundaries via `transactions`).
4. Re-measure at representative volume; record before/after.
5. Set regression alerts on the fixed paths.

## Decision Rules

- Aggregate cost decides priority: a 50ms query at 100 req/s beats a 2s nightly report.
- Plans over intuition — the optimizer's actual choice is the only truth (`indexing` verification discipline).
- Fix the query count before the query speed when both are wrong (N+1 first).
- Representative volume or the measurement lies; test datasets sized accordingly (`seed-data` fixture volumes).
- Scaling the database up is the last fix, taken knowingly, not the first reflex.

## Rules

- Every change carries before/after numbers (unverified until run).
- No correctness sacrifices for speed (isolation downgrades, dropped constraints) without an explicit, recorded decision.
- Fixes land with regression guards.

## Anti-Patterns

- Tuning queries nobody measured.
- Adding indexes without reading the plan (or the write cost — `indexing`).
- "Fixing" N+1 with a cache instead of a join.
- Pool bumped to 500 to hide a transaction holding connections for seconds.
- Benchmarking against empty dev tables and declaring victory.

## Validation Checklist

- [ ] Offenders ranked by aggregate cost with evidence.
- [ ] Each classified (plan / N+1 / unbounded / pool / locks).
- [ ] Fixes at the right layer; delegations made (indexing, transactions, concurrency).
- [ ] Before/after at representative volume recorded.
- [ ] Regression alerts set.

## Definition of Done

Measured offenders fixed at the responsible layer with plan-verified improvements at representative volume, before/after numbers recorded, and regression alerts guarding the fixed paths.

## Related Skills

`indexing`, `transactions`, `concurrency`, `../backend/backend-performance`, `../backend/backend-observability`, `prisma-relational`, `drizzle-relational`, `mongoose-mongodb`, `../../performance-review`.

## Related Knowledge

`../../../knowledge/` (volumes, load profile, database limits).

## Related References

`../../../references/database/performance/` (plan analyses, when populated).

## Context Loading Guidance

- **Requires:** measurements, the implicated queries, schema/index state, volumes.
- **Does not require:** the full codebase, unimplicated queries.
- **May load:** `indexing` (index design), `transactions`/`concurrency` (locks).
- **Stop when:** improvements are proven and guarded.

## Token Efficiency Guidance

Load only the offender list and implicated queries. Report symptom → classification → fix → numbers; skip the tour.
