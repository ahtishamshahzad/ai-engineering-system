---
name: database-selection
description: Use to choose the database (PostgreSQL, MySQL, MongoDB) and — separately — the data layer (Prisma, Drizzle, Mongoose, native driver). Relational is usually right for payments, orders, inventory, reporting, financial data, transactions, strong relations; MongoDB fits highly variable documents with embedded access patterns.
---

# Database Selection

## Purpose

Make **two separate decisions**, in order: (1) the **database** — PostgreSQL, MySQL, or MongoDB — from the data's shape and integrity needs; (2) the **data layer** — Prisma, Drizzle, Mongoose, or native driver — from team ergonomics and control needs. Feeds `../../stack-recommendation`; requires user approval (Gate 2).

## When to Use

- At the start of any project that stores data, or when adding a store.
- When re-evaluating persistence for a migration (`../../migration-planning`).
- **Not** after the database is approved and unchanged.

## Inputs

- Requirement baseline: entities, relations, integrity needs, reporting needs, expected access patterns.
- Existing infrastructure/team experience (`../../existing-project-audit` findings where applicable).

## Discovery Questions

- Does the domain involve **payments, orders, inventory, reporting, financial data, multi-entity transactions, or strongly related entities**? (Any yes → relational is the usual answer.)
- Are document structures **genuinely highly variable** across records, with data naturally read/written as embedded documents? (That's the MongoDB case — not "we haven't designed the schema yet.")
- What queries will exist: cross-entity joins/aggregations (relational strength) vs whole-document reads by key (document strength)?
- For the data layer: does the team want schema-first ergonomics (Prisma), SQL-proximity and control (Drizzle), ODM conveniences (Mongoose), or minimal abstraction (native driver)?

## Responsibilities

- **Database decision first**, on data shape and integrity:
  - **Relational (PostgreSQL/MySQL)** usually preferred for: payments, orders, inventory, reporting, financial data, transactional multi-entity writes, strong relations/constraints. Between them: PostgreSQL for richer types/features, MySQL where team/infra experience or hosting favors it — record the reason.
  - **MongoDB** may be preferred for: highly variable document structures and access patterns that naturally read/write embedded documents as units.
- **Data layer decision second, pairs must be coherent**:
  - Relational DB → **Prisma** (schema-first DX, generated types), **Drizzle** (TypeScript-SQL closeness, control), or **native driver/query builder** (maximum control, more discipline).
  - MongoDB → **Mongoose** (schemas/middleware over documents) or **native driver** (direct control).
- Record both decisions with justification and rejected alternatives; note operational implications (backups, migrations tooling — `database-migrations`, `backup-recovery`).
- Hand off to the matching skills: `relational-schema-design` + `prisma-relational`/`drizzle-relational`, or `document-schema-design` + `mongoose-mongodb`.

## Required Workflow

1. Gather entities, relations, integrity/reporting needs, access patterns.
2. Decide the database from data shape (relational signals vs document signals).
3. Decide the data layer for that database from team/control needs.
4. Record both with trade-offs for Gate 2 approval.
5. Hand off to schema-design + data-layer skills.

## Decision Rules

- **Never conflate the two decisions** — "we use Prisma" is not a database choice; "we use Postgres" doesn't pick the data layer.
- Financial/transactional/relational signals outweigh document-flexibility preferences: integrity failures cost more than schema ceremony.
- "Schemaless" is not a reason for MongoDB — undesigned data isn't variable data (`document-schema-design` still requires design).
- Polyglot persistence (two stores) needs strong, separate justification per store — default is one.
- Team familiarity tiebreaks between adequate options; it doesn't override a shape mismatch.

## Rules

- Recommend, don't pre-select; user approves at Gate 2.
- No installation or schema work before approval.
- Record what would flip each decision.

## Anti-Patterns

- Choosing MongoDB for an orders/payments/inventory domain because iteration feels faster.
- Choosing the database by choosing an ORM first.
- Defaulting to PostgreSQL (or anything) without stating why it fits *this* data.
- "We'll add relations to MongoDB later with manual refs everywhere" — that's a relational domain in denial.
- Two databases because two tutorials.

## Validation Checklist

- [ ] Data shape, integrity, reporting, access patterns gathered.
- [ ] Database decided on data shape (relational signals checked explicitly).
- [ ] Data layer decided separately, coherent with the database.
- [ ] Both recorded with trade-offs + rejected alternatives.
- [ ] Handed to schema-design + data-layer skills; Gate 2 pending.

## Definition of Done

Two recorded, separately justified decisions — database and data layer — coherent as a pair, tied to the domain's data shape and integrity needs, with trade-offs noted and approval pending.

## Related Skills

`../../stack-recommendation`, `relational-schema-design`, `document-schema-design`, `prisma-relational`, `drizzle-relational`, `mongoose-mongodb`, `database-migrations`, `backup-recovery`, `../../backend/existing-backend-audit`.

## Related Knowledge

`../../../knowledge/` (domain model, infra constraints, team experience).

## Related References

`../../../references/database/` (comparison notes, when populated).

## Context Loading Guidance

- **Requires:** entity/relation summary, integrity + reporting needs, access patterns.
- **Does not require:** full schema drafts, ORM docs, application code.
- **May load:** one schema-design skill + one data-layer skill after deciding.
- **Stop when:** both decisions are recorded for Gate 2.

## Token Efficiency Guidance

Decide from the requirements summary; the two-decision table (option, fit, trade-off) is the whole artifact. Don't draft schemas here.
