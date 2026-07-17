# Checklist: Database Change

> Verifiable items for a schema/data change. Schema and migration files are owned by a single database engineer.

## Pass Criteria

- [ ] Change expressed as a **versioned, reviewed migration** (not manual/ad-hoc); reviewed for locks and data safety (`../skills/database/database-migrations`).
- [ ] Constraints enforce the invariants (NOT NULL / UNIQUE / CHECK / FK); money/time/enum types correct.
- [ ] Indexes added from actual query patterns and **plan-verified**; write cost considered (`../skills/database/indexing`).
- [ ] Tenancy/ownership columns present and scoped where applicable.
- [ ] Zero-downtime sequencing (expand → migrate → contract) where required; migration applied before new code serves traffic.
- [ ] **Backup + tested restore** available; rollback ruling recorded (`../skills/database/backup-recovery`).
- [ ] Backfills (if any) are batched, resumable, idempotent, with verification criteria (`../skills/database/data-migration`).
- [ ] Concurrency/transaction implications handled for contended data.

## Fail / Stop

- Manual schema edit outside a migration; missing backup/rollback; unverified indexes; destructive change with no compatibility window.

## Related

Skills: `../skills/database/README.md`. Hook: `../hooks/before-migration.md`. Reference: `../references/backend/database/`.
