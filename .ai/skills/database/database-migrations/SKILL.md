---
name: database-migrations
description: Use to plan schema-migration discipline — versioned immutable migration files, review of generated SQL, deploy-time ordering, zero-downtime expand/contract changes, rollback reality, and environment drift prevention. Data backfills are the data-migration skill.
---

# Database Migrations

## Purpose

Evolve the schema safely: every change a versioned, reviewed, immutable migration; applied in order everywhere; shaped so deploys don't break running code. Moving/transforming *data* is `data-migration`; this skill owns *schema* change mechanics.

## When to Use

- Whenever the schema changes, and when establishing the migration workflow.
- **Not** for backfills/transformations (`data-migration`) or seed content (`seed-data`).

## Inputs

- Schema change intent (`relational-schema-design` / `document-schema-design` output).
- Tooling from the data layer (`prisma-relational` / `drizzle-relational` / `mongoose-mongodb` index builds), deploy flow (`../backend/backend-deployment`).

## Discovery Questions

- What applies migrations in each environment (deploy step — automated, ordered, once)?
- Is zero-downtime required (drives expand/contract), and what's the table size (locks!)?
- What's the actual rollback story — down migrations, or roll-forward-only with code rollback?

## Responsibilities

- Establish the workflow: migrations as **versioned files in the repo**, generated-or-written then **reviewed as code** (locks, constraint validity, index build behavior), **immutable once applied** beyond dev — fixes are new migrations.
- Order migrations with deploys: applied **before** new code serves traffic (`backend-deployment`); exactly-once application per environment (migration table/lock).
- Shape changes for **zero-downtime** where required — **expand → migrate → contract**:
  - additive first (new nullable column/table/index), old code keeps working;
  - backfill via `data-migration` (batched, not in the schema migration);
  - constraints tightened (NOT NULL, uniques) only after backfill;
  - destructive contraction (drops/renames) only after no deployed code references the old shape.
- Respect **lock behavior** on big tables: concurrent index builds where the engine offers them, constraint validation split from creation, batched changes — a migration that locks a hot table for minutes is an outage.
- Define the **rollback ruling**: schema is usually roll-forward-only (down migrations lose data or lie) — recorded explicitly; code rolls back, schema rolls forward (`backend-deployment` alignment).
- Prevent drift: CI compares schema-as-migrated vs schema-as-defined; no manual production edits — ever; MongoDB index/validator changes ride the same versioned mechanism.

## Required Workflow

1. Express the schema change as migration file(s); review the SQL/operations for locks and correctness.
2. Classify: additive / tightening / destructive → apply expand/contract sequencing if zero-downtime.
3. Coordinate backfills with `data-migration` between expand and tighten.
4. Verify against a production-like dataset (timing, locks) — staging first.
5. Apply via the deploy pipeline; confirm drift checks pass.

## Decision Rules

- Rename = add + backfill + contract, never in-place, under zero-downtime.
- NOT NULL/unique constraints arrive only after data provably satisfies them.
- Long operations (index builds, validations) use the engine's non-blocking modes or run in maintenance windows — chosen, not stumbled into.
- Down migrations exist only where they're honest; otherwise the ruling is roll-forward and everyone knows it.

## Rules

- No schema change outside a migration file; no editing applied migrations.
- Every migration reviewed (locks, data safety) before merge (`../../code-review`).
- Destructive migrations name the approval that authorized them (`../../system/QUALITY_GATES.md`).

## Anti-Patterns

- `db push`/auto-sync against shared environments.
- Editing an applied migration "because it was wrong."
- Schema migration + heavy backfill in one transaction, locking the table.
- Dropping a column while old code still reads it.
- Manual production hotfix SQL that no migration records (drift).

## Validation Checklist

- [ ] Change captured as versioned, reviewed migration file(s).
- [ ] Applied-before-code ordering; exactly-once mechanism.
- [ ] Expand/contract sequencing where zero-downtime required.
- [ ] Lock behavior assessed on realistic data size.
- [ ] Rollback ruling recorded (roll-forward vs honest down).
- [ ] Drift check in CI; no manual edits anywhere.

## Definition of Done

A reviewed, versioned migration path for the change — safely ordered against deploys, lock-aware, expand/contract where needed, with an explicit rollback ruling and drift protection.

## Related Skills

`data-migration`, `seed-data`, `relational-schema-design`, `document-schema-design`, `prisma-relational`, `drizzle-relational`, `mongoose-mongodb`, `../backend/backend-deployment`, `backup-recovery`, `../../migration-planning`.

## Related Knowledge

`../../../knowledge/` (table sizes, downtime tolerance, environment list).

## Related References

`../../../references/database/migrations/` (sequencing playbooks, when populated).

## Context Loading Guidance

- **Requires:** the schema change intent, data size, deploy flow, downtime tolerance.
- **Does not require:** application feature code, full schema history.
- **May load:** `data-migration` (backfill leg), `../backend/backend-deployment` (ordering).
- **Stop when:** the migration files + sequencing + ruling are recorded.

## Token Efficiency Guidance

Review the migration diff, not the whole schema. The expand/migrate/contract step list per change is the artifact.
