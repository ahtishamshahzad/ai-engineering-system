---
name: indexing
description: Use to design indexes from actual query patterns — composite column order, covering/partial/unique indexes, foreign-key and tenant-key coverage, MongoDB compound/ESR ordering, verification via query plans, and write-cost awareness.
---

# Indexing

## Purpose

Derive indexes from the queries the system actually runs: the right columns in the right order, verified by query plans — because a missing index is the most common database performance failure and an unused one is pure write tax.

## When to Use

- After schema design, alongside query-pattern definition; when `database-performance` finds slow queries.
- **Not** speculatively per column "just in case."

## Inputs

- Query inventory: filters, sorts, joins per endpoint/job (from `../../backend/rest-api-design` list endpoints, `../../backend/backend-performance` findings).
- Schema + data layer (`prisma-relational`/`drizzle-relational`/`mongoose-mongodb` declare them), write-volume profile.

## Discovery Questions

- What are the top queries by frequency and by cost — their WHERE/ORDER BY/JOIN shapes?
- Which are tenant-scoped (tenant key leads composites — every scoped query filters it, `../../backend/ownership-authorization`)?
- What's the read/write ratio per table — how much index tax can hot-write tables afford?

## Responsibilities

- Build the **query → index map**: each hot query shape gets an index that serves it, or a recorded reason it doesn't need one (tiny table, rare query).
- Design **composites by selectivity and role**: equality filters first, then sort columns, then range — one composite often serves several queries; leftmost-prefix reuse beats near-duplicate indexes. (MongoDB: same discipline — equality/sort/range ordering on compound indexes.)
- Cover the structurals: **foreign-key columns** (join + cascade performance — engines don't all auto-index FKs), **unique indexes as constraints** (`relational-schema-design` invariants), **tenant keys leading** scoped composites.
- Use targeted forms where they earn it: **partial/filtered indexes** (soft-delete-aware uniqueness, status subsets), **covering indexes** for hot read paths — each justified by a query, not fashion.
- **Verify with plans**: EXPLAIN/`.explain()` on representative data volume — index *used*, not just present; seq-scan on big tables in hot paths is the failure signal.
- Account for **write cost**: every index slows insert/update; audit for unused indexes (engine stats) and remove dead weight.
- Plan **build/rollout**: concurrent builds on live tables, index changes as migrations (`database-migrations`), Mongoose autoIndex off in production (`mongoose-mongodb`).

## Required Workflow

1. Inventory query shapes with frequency/cost.
2. Map queries → proposed indexes (composites ordered deliberately; reuse prefixes).
3. Add structural coverage (FKs, uniques, tenant keys).
4. Verify with plans at realistic volume; iterate.
5. Ship as migrations with safe build strategy; schedule unused-index review.

## Decision Rules

- No index without a query; no hot query without a plan check.
- Column order is the design: an index that doesn't match filter+sort shape is a near-miss, not a win.
- Low-selectivity single columns (booleans, small enums) rarely deserve their own index — composite or partial instead.
- LIKE '%x%', functions over columns, and type mismatches defeat indexes — fix the query shape or use the matching index type (expression/text/GIN where the engine offers).
- Uniqueness belongs in a unique index, not an application check (`concurrency`).

## Rules

- Index changes are migrations, reviewed, concurrent-built on live tables.
- Verification on realistic data volume — empty-table plans lie.
- Keep the query → index map current; it's the audit trail.

## Anti-Patterns

- Indexing every column on principle.
- Five single-column indexes where one composite serves.
- Composite ordered by intuition (range column first, equality last).
- Unverified "should be indexed now" claims — plan or it didn't happen.
- Tenant-scoped tables whose indexes don't lead with the tenant key.
- Never deleting an index that stopped being used two features ago.

## Validation Checklist

- [ ] Query inventory with frequency/cost.
- [ ] Query → index map; composites ordered equality→sort→range.
- [ ] FK, unique, tenant-key coverage.
- [ ] Partial/covering forms justified per query.
- [ ] Plans verified at realistic volume.
- [ ] Migration + concurrent build; unused-index review scheduled.

## Definition of Done

A recorded query → index map with deliberately ordered, plan-verified indexes covering hot paths and structural needs, shipped as safe migrations, with write cost and unused-index review accounted for.

## Related Skills

`database-performance`, `relational-schema-design`, `document-schema-design`, `database-migrations`, `concurrency` (unique constraints), `prisma-relational`, `drizzle-relational`, `mongoose-mongodb`, `../../backend/backend-performance`.

## Related Knowledge

`../../../knowledge/` (query patterns, volumes, read/write ratios).

## Related References

`../../../references/database/performance/` (plan analyses, when populated).

## Context Loading Guidance

- **Requires:** query shapes with frequency, schema, volume profile.
- **Does not require:** application logic, full schema history.
- **May load:** `database-performance` (slow-query source), `database-migrations` (rollout).
- **Stop when:** the map is plan-verified and shipped as migrations.

## Token Efficiency Guidance

The query → index table is the artifact. Paste plans only for contested queries; summarize the rest as used/not-used.
