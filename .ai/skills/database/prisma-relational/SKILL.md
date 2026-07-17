---
name: prisma-relational
description: Use to plan Prisma over PostgreSQL/MySQL — expressing the approved schema in the Prisma schema, migration workflow, client usage patterns (select scope, N+1 avoidance, transactions), and where Prisma's abstractions end (raw SQL escape hatch).
---

# Prisma (Relational)

## Purpose

Express the approved relational schema through Prisma and use its client well: schema-as-source, generated types, disciplined queries, and a clear line where raw SQL takes over. The schema *design* comes from `relational-schema-design`; this skill is its Prisma expression.

## When to Use

- After Prisma + a relational DB were approved (`database-selection`).
- **Not** for schema design decisions (upstream) or Drizzle/Mongoose projects.

## Inputs

- Approved schema design (entities, constraints, types, relations).
- Migration/environment workflow expectations (`database-migrations`).

## Discovery Questions

- Does the schema use features Prisma models natively (enums, composite uniques, referential actions) vs ones needing raw SQL in migrations (partial indexes, CHECK constraints, triggers)?
- Where do generated types flow (service layer boundaries — `../backend/backend-api-architecture` says entities ≠ DTOs)?
- What query hot paths exist (drives select/include discipline)?

## Responsibilities

- Express the design in `schema.prisma`: types, relations with explicit referential actions (`onDelete`), `@@unique`/`@@index`, enums — mapping every constraint the design demands; what Prisma's DSL can't express (CHECKs, partial indexes) goes into migration SQL deliberately, not dropped.
- Own the **migration workflow** with `database-migrations`: `migrate dev` in development, generated SQL **reviewed like code**, `migrate deploy` in environments; no `db push` beyond throwaway prototyping; drift detection in CI.
- Set **client usage patterns**:
  - `select`/`include` scoped to need — no default full-entity fetches leaking through APIs (`../backend/rest-api-design` payload discipline);
  - relation loading strategy per hot path — avoid N+1 (batched `include`, or restructured queries);
  - `$transaction` for multi-write invariants (`transactions` owns the boundaries);
  - pagination via cursor patterns on stable keys;
  - singleton client with pool sizing matched to deployment (`database-performance`).
- Define the **raw-SQL escape hatch** policy: `$queryRaw` (parameterized only — `database-security`) for reporting/bulk/window queries where the client abstraction fights; typed and reviewed.
- Keep generated client/types out of domain contracts: repository/service boundary maps Prisma models → domain/DTO shapes.

## Required Workflow

1. Translate the approved design into `schema.prisma`; list what needs raw-SQL migration additions.
2. Wire the migration workflow (dev/deploy/CI drift check).
3. Define client patterns (select scope, N+1 strategy, transactions, pagination).
4. Set the raw-SQL policy and pool config.
5. Verify constraints landed in SQL (inspect generated migrations) — not just in the DSL.

## Decision Rules

- The generated migration SQL is the truth — review it; the DSL is shorthand.
- Any constraint Prisma can't express still ships (raw SQL in the migration) — the design isn't negotiable downward to the tool.
- `db push` never touches shared environments.
- N+1s hide behind lazy convenience: hot paths get explicit query shape review (`database-performance`).

## Rules

- All raw queries parameterized; string interpolation into SQL is a defect.
- Migration files are immutable once applied beyond dev (`database-migrations`).
- Client version and engine pinned; upgrades follow `../../dependency-audit`.

## Anti-Patterns

- Designing the schema *in* Prisma DSL ad hoc, skipping the design skill.
- Dropping CHECK/partial-index requirements because the DSL lacks them.
- `findMany` with no `select`, no `take` — full rows, unbounded.
- Prisma models serialized straight out of controllers.
- Per-request `new PrismaClient()` exhausting connections.

## Validation Checklist

- [ ] Design fully expressed (DSL + raw-SQL supplements listed).
- [ ] Migration workflow wired; generated SQL reviewed; CI drift check.
- [ ] Client patterns set: select scope, N+1 plan, transactions, pagination.
- [ ] Raw-SQL policy (parameterized, reviewed) recorded.
- [ ] Pool sizing matched to deployment.
- [ ] Models mapped to DTOs at the boundary.

## Definition of Done

The approved schema fully expressed through Prisma (with deliberate raw-SQL supplements), a reviewed migration workflow, recorded client usage patterns that prevent N+1/overfetch/connection exhaustion, and a parameterized-only raw-SQL policy.

## Related Skills

`database-selection`, `relational-schema-design`, `database-migrations`, `transactions`, `indexing`, `database-performance`, `database-security`, `seed-data`, `../backend/backend-api-architecture`.

## Related Knowledge

`../../../knowledge/` (hot paths, environment workflow).

## Related References

`../../../references/database/prisma/` (patterns, when populated).

## Context Loading Guidance

- **Requires:** approved schema design, migration workflow expectations, hot paths.
- **Does not require:** re-deciding schema/database choices, app feature code.
- **May load:** `database-migrations` (workflow), `transactions` (boundaries).
- **Stop when:** schema expression + workflow + client patterns are recorded.

## Token Efficiency Guidance

Work from the schema-design table; don't paste the whole `schema.prisma` into discussion — reference models by name and show only contested mappings.
