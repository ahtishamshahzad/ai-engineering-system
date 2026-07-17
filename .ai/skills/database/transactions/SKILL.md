---
name: transactions
description: Use to define transaction boundaries — which multi-write invariants must be atomic, where the boundary lives (service layer), isolation levels and retry handling, keeping external calls out of transactions, and the outbox pattern for commit-coupled side effects.
---

# Transactions

## Purpose

Make multi-write invariants explicitly atomic: identify them, wrap them in deliberate transaction boundaries, choose isolation consciously, and keep slow/external work outside — so the database never holds a half-truth and transactions never hold a connection hostage.

## When to Use

- Whenever one logical operation writes more than one row/table/document.
- When money, inventory, or counters move (`database-selection` relational signals).
- **Not** for single-document/row writes (already atomic) — and see `concurrency` for contention *between* transactions.

## Inputs

- The domain invariants: which writes must succeed or fail together.
- Data layer transaction API (`prisma-relational` `$transaction` / `drizzle-relational` `db.transaction` / `mongoose-mongodb` sessions), architecture layer map (`../backend/backend-api-architecture`).

## Discovery Questions

- Which operations span multiple writes with an invariant between them (order + items + stock decrement; transfer debit + credit)?
- What sits inside the operation today that doesn't belong in a transaction (emails, HTTP calls, file writes)?
- What isolation does each invariant need — and what happens on serialization failure/deadlock (retry policy)?

## Responsibilities

- Build the **invariant inventory**: every multi-write operation, its atomicity requirement, and its failure semantics.
- Place boundaries in the **service layer** (one transaction per use-case operation), passed explicitly through the data layer — not implicit per-query autocommit sprinkled with hope, not controller-level try/catch.
- Keep transactions **short and pure**: reads needed for the decision, then writes, then commit. **No external calls, queue publishes, emails, or slow computation inside** — a transaction holding a connection during a 3s HTTP call is a pool outage in waiting (`database-performance`).
- Couple side effects to commit with the **outbox pattern** (event/job row written in the same transaction; relay publishes after commit — `../backend/background-jobs`) or enqueue-after-commit with the miss-risk recorded.
- Choose **isolation deliberately**: default READ COMMITTED semantics vs stronger (REPEATABLE READ/SERIALIZABLE) where invariants demand (balance checks, uniqueness-ish decisions); serialization failures and deadlocks get **bounded retries with jitter** at the service boundary — the operation must be idempotent-in-effect to retry (`concurrency`).
- MongoDB: single-document atomicity first (`document-schema-design` embeds); multi-document sessions/transactions only for flagged invariants — routine need signals wrong store or wrong shape.
- Test the boundary: mid-operation failure rolls back everything; partial-commit states unrepresentable (`../backend/backend-integration-testing`).

## Required Workflow

1. Inventory multi-write invariants and their failure semantics.
2. Draw one boundary per use-case in the service layer.
3. Evict external/slow work; wire outbox/after-commit for side effects.
4. Set isolation + retry policy per invariant.
5. Test: injected mid-transaction failure leaves no partial state; retried operation converges.

## Decision Rules

- If two writes must agree, they share a transaction — "usually works" is a data-corruption schedule.
- The transaction is as long as the invariant requires and no longer.
- Read-modify-write races inside transactions still need locking/versioning — isolation isn't magic (`concurrency` owns the patterns).
- Side effects that must-follow-commit go through the outbox; "we'll send it after, probably" loses events on crash.

## Rules

- Boundaries are visible in code (named service methods), not spread across layers.
- Every retry-on-conflict path is bounded and logged (`../backend/backend-observability`).
- No transaction spans a user interaction or awaits an external service.

## Anti-Patterns

- Order created, stock decrement failed, both kept — no transaction.
- HTTP/queue/email calls inside the transaction.
- One giant transaction wrapping a whole batch job (`data-migration` batches instead).
- Catching a serialization failure and returning success.
- Nested ad-hoc transactions with unclear commit ownership.

## Validation Checklist

- [ ] Invariant inventory recorded.
- [ ] One service-layer boundary per multi-write use case.
- [ ] No external/slow calls inside; outbox/after-commit wired for side effects.
- [ ] Isolation + bounded retry per invariant.
- [ ] Mid-failure rollback and retry convergence tested.

## Definition of Done

Every multi-write invariant runs inside a deliberate, short, service-layer transaction with chosen isolation, bounded retries, commit-coupled side effects, and tests proving no partial state survives failure.

## Related Skills

`concurrency`, `../backend/background-jobs` (outbox relay), `../backend/backend-api-architecture`, `relational-schema-design`, `document-schema-design`, `database-performance` (connection hold), `../backend/backend-integration-testing`.

## Related Knowledge

`../../../knowledge/` (domain invariants, failure semantics).

## Related References

`../../../references/database/transactions/` (boundary patterns, when populated).

## Context Loading Guidance

- **Requires:** invariant inventory, data-layer transaction API, layer map.
- **Does not require:** unrelated read paths, full schema.
- **May load:** `concurrency` (contention), `../backend/background-jobs` (outbox).
- **Stop when:** boundaries, isolation, retries, and tests are recorded.

## Token Efficiency Guidance

The invariant → boundary → isolation → retry table is the artifact; reason about the contested operations, not every CRUD write.
