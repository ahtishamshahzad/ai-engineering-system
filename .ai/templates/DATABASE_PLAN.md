# Database Plan — <project/feature>

> Fill-in template. Records the two decisions (database + data layer) and the schema/integrity plan (`../skills/database/database-selection`, schema-design skills).

- **Date:** <YYYY-MM-DD> · **Status:** draft | approved

## Decisions

- **Database:** <PostgreSQL / MySQL / MongoDB> — <why, from data shape/integrity>
- **Data layer:** <Prisma / Drizzle / Mongoose / native> — <why>
- **Coherent pair:** [ ] confirmed

## Schema

| Entity / collection | Keys | Relationships | Key constraints (invariants) | Tenancy scope |
|---------------------|------|---------------|------------------------------|---------------|
| <> | <> | <> | NOT NULL / UNIQUE / CHECK / FK | <owner/org> |

- **Money/time/enum types:** <decimal-or-minor-units for money; timestamptz/UTC; enum approach>

## Indexing (from query patterns)

| Query shape | Index | Verified by plan |
|-------------|-------|------------------|
| <> | <> | [ ] |

## Transactions / Concurrency

- **Multi-write invariants:** <boundary; isolation; retry>
- **Contended data / races:** <mechanism: atomic / optimistic / pessimistic / constraint / idempotency>

## Migrations & Backup

- **Migration flow:** <expand/contract; sole owner: database engineer>
- **Backup/RPO-RTO + tested restore:** <>

## Related

- Skills: `../skills/database/README.md`. Security lens: `../skills/security/database-security`.
