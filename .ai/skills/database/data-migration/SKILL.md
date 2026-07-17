---
name: data-migration
description: Use to plan data movement/transformation — backfills for schema change, reshaping documents, moving between stores — batched and resumable, idempotent, verified by counts/checksums, with dual-write/dual-read cutover for live systems and a real rollback path.
---

# Data Migration

## Purpose

Move or transform existing data safely: backfills that accompany schema change, document reshaping, store-to-store moves — designed for interruption, re-run, verification, and rollback, because data migrations fail midway in production, on real data, at volume.

## When to Use

- Backfilling new columns/shapes (between `database-migrations`' expand and contract steps).
- Reshaping documents (`document-schema-design` changes), merging/splitting entities, moving stores.
- **Not** for schema DDL itself (`database-migrations`) or environment seed content (`seed-data`).

## Inputs

- Source and target shapes + the transformation rules (including edge/legacy values).
- Data volume, write traffic on affected tables/collections, downtime tolerance.
- `../../migration-planning` for the surrounding project when the migration is the project.

## Discovery Questions

- Volume and rate: how many rows/documents, how fast can we process without hurting production (`database-performance` headroom)?
- Is the data live (writes during migration) — dual-write/dual-read needed, or is a quiet window real?
- What are the dirty-data cases (nulls, legacy encodings, orphans) and their rulings?
- How is success *proven* (counts, checksums, sampled field comparison) — and what's the rollback if proof fails?

## Responsibilities

- Write the **transformation spec**: field mappings, derivations, and an explicit ruling for every dirty-data case found by *profiling the real data first* — surprises belong in profiling, not mid-run.
- Design the runner: **batched** (bounded transactions, no table-length locks), **rate-limited**, **resumable** (checkpointed progress — crash at row 3M restarts at 3M), **idempotent** (re-processing a batch converges; upsert/merge semantics).
- Handle **live traffic**: expand schema first (`database-migrations`), then either dual-write (app writes old+new during backfill) or ordered backfill-then-switch; reads cut over only after verification; contract last.
- **Verify**: row/document counts per cohort, checksums or field-level sampling, application-level invariant checks (`transactions`-era invariants still hold); verification is a step with pass/fail criteria, not a vibe.
- Plan **rollback**: backup/snapshot point (`backup-recovery`) taken before mutation; reversible switch (reads back to old path) until contract; after contract, roll-forward-only — stated in advance.
- Log progress + errors per batch (`../backend/backend-observability`); quarantine-and-continue vs halt-on-error ruled per error class.
- Rehearse on a production-scale copy: timing, locks, error rates — a migration first run in production is a gamble, not a plan.

## Required Workflow

1. Profile source data; write the transformation spec with dirty-data rulings.
2. Build the batched/resumable/idempotent runner.
3. Sequence with schema steps and live-traffic strategy (dual-write windows).
4. Rehearse on production-scale data; measure duration and impact.
5. Snapshot, run with monitoring, verify against criteria.
6. Cut reads over; hold the old path until stability; contract per `database-migrations`.

## Decision Rules

- Batch size balances lock time vs total duration — measured in rehearsal, not guessed.
- Idempotency is non-negotiable: every batch must be safely re-runnable.
- Verification criteria are defined *before* the run; "it looks right" is not criteria.
- Dirty data gets explicit rulings (transform/quarantine/reject) — silent coercion corrupts.
- If the old path can't be kept readable during cutover, downtime is being chosen — record it.

## Rules

- Snapshot before any mutating run.
- Never run unrehearsed against production.
- Progress observable while running; halt criteria pre-agreed.

## Anti-Patterns

- `UPDATE everything` in one transaction on a hot table.
- Non-resumable scripts that must restart from zero after a crash.
- Migrating unprofiled data and discovering legacy encodings at row 2 million.
- Declaring success from a completed run with no count/checksum verification.
- Dropping the old column/collection the same day reads switched.

## Validation Checklist

- [ ] Source profiled; transformation spec with dirty-data rulings.
- [ ] Runner batched, rate-limited, checkpointed, idempotent.
- [ ] Live-traffic strategy sequenced with schema expand/contract.
- [ ] Rehearsed at production scale; duration/impact measured.
- [ ] Snapshot taken; verification criteria defined and passed.
- [ ] Rollback path stated per phase; old path held until stable.

## Definition of Done

A rehearsed, batched, resumable, idempotent migration with a written transformation spec, pre-agreed verification criteria that passed, a snapshot-backed rollback path per phase, and a clean cutover — recorded end to end.

## Related Skills

`database-migrations`, `backup-recovery`, `../../migration-planning`, `transactions`, `concurrency`, `database-performance`, `../backend/backend-observability`, `seed-data`.

## Related Knowledge

`../../../knowledge/` (data volumes, traffic patterns, dirty-data rulings).

## Related References

`../../../references/database/migrations/` (runner patterns, when populated).

## Context Loading Guidance

- **Requires:** source/target shapes, volume + traffic profile, downtime tolerance.
- **Does not require:** unrelated schema areas, application feature plans.
- **May load:** `database-migrations` (sequencing), `backup-recovery` (snapshot).
- **Stop when:** spec, runner design, rehearsal results, and verification are recorded.

## Token Efficiency Guidance

The transformation spec table (source → rule → target, dirty-case rulings) plus the phase sequence is the artifact; profile summaries beat row dumps.
