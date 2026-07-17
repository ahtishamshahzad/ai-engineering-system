---
name: drizzle-relational
description: Use to plan Drizzle over PostgreSQL/MySQL — TypeScript table definitions expressing the approved schema, drizzle-kit migration workflow, SQL-proximate query patterns (joins, prepared statements, transactions), and type flow to the app boundary.
---

# Drizzle (Relational)

## Purpose

Express the approved relational schema in Drizzle's TypeScript table definitions and use its SQL-proximate query model well — Drizzle is chosen for control and closeness to SQL; this skill keeps that control disciplined. Schema *design* comes from `relational-schema-design`.

## When to Use

- After Drizzle + a relational DB were approved (`database-selection`).
- **Not** for schema design (upstream) or Prisma/Mongoose projects.

## Inputs

- Approved schema design (entities, constraints, types, relations).
- Migration/environment expectations (`database-migrations`); hot query paths.

## Discovery Questions

- Which dialect helpers/column types map the design (and where are DB-specific features needed — e.g. partial indexes, CHECKs — that Drizzle *can* express)?
- How will relational reads be written (query builder joins vs the relational query API) per hot path?
- Where do inferred types flow, and where are they mapped to DTOs?

## Responsibilities

- Express the design in table definitions: columns/types, FKs with referential actions, unique/composite constraints, CHECKs, indexes (including partial where the design says so) — Drizzle's SQL closeness means little excuse for dropped constraints.
- Own the **migration workflow** with `database-migrations`: `drizzle-kit generate` producing SQL migrations **reviewed like code**, applied via the migrator in environments; hand-edited SQL allowed-and-reviewed for what generation misses; drift checked in CI.
- Set **query patterns**:
  - joins written explicitly (that's the point of Drizzle) — relational query API where it stays efficient; hot paths get their SQL shape reviewed;
  - selected columns scoped to need; pagination on stable keys;
  - prepared statements for hot repeated queries;
  - `db.transaction` for multi-write invariants (`transactions` owns boundaries);
  - one pooled client, sized per deployment (`database-performance`).
- Keep the boundary clean: inferred row types (`$inferSelect`) are data-layer types; map to domain/DTO shapes at the service boundary (`../../backend/backend-api-architecture`).
- Use `sql` template escape hatches parameterized-only (`database-security`).

## Required Workflow

1. Translate the design into table definitions; confirm every constraint expressed.
2. Wire generate → review → migrate flow + CI drift check.
3. Define query patterns per hot path (join shape, select scope, prepared).
4. Set transaction and pooling conventions.
5. Verify migration SQL matches the design (constraints present, indexes named).

## Decision Rules

- Drizzle was chosen for SQL control — exercise it: constraints in-schema, joins explicit, generated SQL read.
- The relational query API is convenience; when its generated SQL disappoints on a hot path, drop to the builder — measured, not assumed (`database-performance`).
- Schema TypeScript files are the source; nobody edits the database by hand around them.
- Raw `sql` fragments carry parameters, never interpolated strings.

## Rules

- Migrations immutable once applied beyond dev (`database-migrations`).
- Every table definition change goes through generate + review — no silent drift.
- Type inference doesn't replace DTO mapping at the boundary.

## Anti-Patterns

- Treating Drizzle like an ORM black box and never reading its SQL.
- Constraints "handled in app code" that the design assigned to the schema.
- `select()` star-everything on wide tables in list endpoints.
- Interpolating user input into `sql` templates.
- Skipping migration review because "it's generated."

## Validation Checklist

- [ ] Design fully expressed in table definitions (constraints, indexes, actions).
- [ ] generate → review → migrate flow wired; CI drift check.
- [ ] Hot-path query shapes defined (joins, select scope, prepared).
- [ ] Transactions + pooling conventions set.
- [ ] Raw fragments parameterized-only.
- [ ] Row types mapped to DTOs at the boundary.

## Definition of Done

The approved schema fully expressed in reviewed Drizzle definitions and migrations, recorded query/transaction/pooling patterns for the hot paths, and a parameterized-only raw-SQL policy — with types flowing cleanly to a mapped boundary.

## Related Skills

`database-selection`, `relational-schema-design`, `database-migrations`, `transactions`, `indexing`, `database-performance`, `database-security`, `seed-data`, `../../backend/backend-api-architecture`.

## Related Knowledge

`../../../knowledge/` (hot paths, dialect specifics).

## Related References

`../../../references/database/drizzle/` (patterns, when populated).

## Context Loading Guidance

- **Requires:** approved schema design, migration expectations, hot paths.
- **Does not require:** re-deciding schema/database, app feature code.
- **May load:** `database-migrations`, `database-performance` (hot-path review).
- **Stop when:** expression + workflow + query patterns are recorded.

## Token Efficiency Guidance

Reference tables by name against the design doc; show SQL only for contested hot paths. The constraint-coverage checklist is the core artifact.
