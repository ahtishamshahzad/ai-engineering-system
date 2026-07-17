---
name: concurrency
description: Use to handle concurrent access to shared data — finding read-modify-write races, atomic updates, optimistic versioning vs pessimistic locks, unique constraints over check-then-insert, idempotency keys for retried operations, and lost-update protection.
---

# Concurrency

## Purpose

Make simultaneous access safe: identify where concurrent requests, jobs, and retries touch the same data, and apply the right mechanism — atomic updates, optimistic versioning, pessimistic locks, constraints — so races become impossible rather than improbable. `transactions` makes one operation atomic; this skill handles operations *colliding*.

## When to Use

- Wherever two actors can touch the same row/document at once: counters, balances, stock, status flows, double-submits, parallel jobs.
- **Not** for single-writer data or transaction-boundary design (`transactions`).

## Inputs

- Contended-data inventory: which entities get concurrent writes, from which paths (requests, jobs, webhooks).
- Data layer capabilities; retry sources (`../../backend/background-jobs` at-least-once, user double-clicks, `../../backend/webhooks` redelivery).

## Discovery Questions

- Where does read-modify-write happen (load → compute → save)? Each is a lost-update candidate.
- Which flows race against duplicates: double-submit, retried jobs, redelivered webhooks?
- What contention level: rare (optimistic fits) vs hot-spot rows like shared counters/stock (pessimistic/atomic fits)?
- What must never go negative/duplicate/skip states (invariants under concurrency)?

## Responsibilities

- Inventory races: **lost updates** (read-modify-write), **check-then-act** (uniqueness/quotas checked in app code), **double-execution** (retries/redelivery), **state-machine jumps** (parallel status transitions).
- Prefer **atomic single-statement updates**: `UPDATE ... SET stock = stock - 1 WHERE stock >= 1` / `$inc` with a guard — the database serializes; the app never computes on stale reads for hot counters.
- Use **optimistic concurrency** (version column / `updatedAt` compare-and-set) for low-contention entities users edit: conflict → bounded retry or user-facing conflict resolution (409 per `../../backend/backend-error-handling`).
- Use **pessimistic locking** (`SELECT ... FOR UPDATE` inside a `transactions` boundary) for hot invariants where retry storms would thrash — held briefly, acquired in consistent order (deadlock avoidance).
- Enforce uniqueness/quotas with **database constraints**, not check-then-insert — handle the violation error as the race signal (`relational-schema-design`, `indexing` unique indexes).
- Design **idempotency**: keys on unsafe-to-repeat operations (payments, sends — `../../backend/rest-api-design`), dedup on webhook/job IDs (`../../backend/webhooks`, `../../backend/background-jobs`), state checks making re-execution converge.
- Gate state machines: transitions as guarded atomic updates (`WHERE status = 'pending'`) — zero-row-updated means the race lost, handle it.
- Distributed coordination (single scheduled runner, cross-instance locks) → `../../backend/scheduled-jobs` mechanisms; don't reinvent with sleeps.

## Required Workflow

1. Inventory contended entities and race classes per path.
2. Choose the mechanism per case (atomic / optimistic / pessimistic / constraint / idempotency key).
3. Define conflict behavior: retry (bounded, jittered), 409 to the user, or converge silently.
4. Implement; keep lock scopes minimal and ordered.
5. Test under real concurrency: parallel writers, duplicate deliveries, double-submits (`../../backend/backend-integration-testing`).

## Decision Rules

- The database's atomicity beats application cleverness — push the race to an atomic statement or constraint whenever possible.
- Optimistic for low contention + human edits; pessimistic for hot invariants; atomic statements for counters — contention level decides, not preference.
- Any check the app does before writing (unique? quota? balance?) is a race unless the write re-verifies atomically.
- Every retry path assumes the previous attempt may have succeeded — idempotency first, then retry.

## Rules

- No lost-update-prone read-modify-write on contended data.
- Locks acquired in documented, consistent order; held inside one transaction, briefly.
- Concurrency tests exercise actual parallelism — sequential tests prove nothing here.

## Anti-Patterns

- `const s = await get(); s.stock -= 1; await save(s);` on shared stock.
- `if (!(await exists(email))) insert(email)` — the classic duplicate race.
- Locking rows then calling a payment API while holding the lock.
- Unbounded conflict-retry loops melting the database.
- Treating "we've never seen it happen" as absence of the race.

## Validation Checklist

- [ ] Race inventory per entity/path (lost update, check-then-act, double-exec, state jump).
- [ ] Mechanism chosen per case with contention rationale.
- [ ] Uniqueness/quotas constraint-enforced; violations handled.
- [ ] Idempotency on all retry-exposed operations.
- [ ] Conflict behavior defined (retry/409/converge).
- [ ] Parallel-execution tests in place.

## Definition of Done

A recorded race inventory with a chosen mechanism per case — atomic updates, versioning, ordered short locks, constraints, idempotency keys — conflict behavior defined, and genuinely parallel tests proving invariants hold.

## Related Skills

`transactions`, `relational-schema-design` (constraints), `indexing` (unique indexes), `../../backend/background-jobs`, `../../backend/webhooks`, `../../backend/scheduled-jobs` (distributed locks), `../../backend/backend-error-handling` (409), `../../backend/backend-integration-testing`.

## Related Knowledge

`../../../knowledge/` (contention profile, invariants).

## Related References

`../../../references/database/transactions/` (race patterns, when populated).

## Context Loading Guidance

- **Requires:** contended-entity inventory, retry sources, data-layer capabilities.
- **Does not require:** uncontended CRUD paths, full schema.
- **May load:** `transactions` (boundaries), `../../backend/background-jobs` (retry semantics).
- **Stop when:** mechanisms, conflict behavior, and parallel tests are recorded.

## Token Efficiency Guidance

The race → mechanism table is the artifact; analyze the contended paths only — auditing every write for races wastes context.
