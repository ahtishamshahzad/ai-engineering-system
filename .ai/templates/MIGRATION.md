# Migration — <what is migrating>

> Fill-in work-item template. Incremental, reversible, backed by backup + rehearsal + verification + cutover (`../skills/migration-planning`, `../skills/database/data-migration`).

- **Date:** <YYYY-MM-DD> · **Type:** schema | data | framework | infra · **Status:** planned | rehearsed | done

## Source → Target

<From what, to what. The transformation at a high level.>

## Transformation Spec

| Source | Rule | Target | Dirty-data ruling |
|--------|------|--------|-------------------|
| <> | <> | <> | transform/quarantine/reject |

## Sequencing

- **Pattern:** expand → migrate → contract (for zero-downtime) | window
- **Live traffic:** dual-write? backfill-then-switch?

## Safety

- **Backup/snapshot taken:** [ ] · **Restore tested:** [ ]
- **Runner:** batched · resumable · idempotent — [ ] all three
- **Rehearsed at production scale:** [ ]

## Verification Criteria (pre-agreed)

- <Counts / checksums / field sampling / invariant checks — pass/fail>

## Rollback

- **Ruling:** reversible | roll-forward-only — <why>
- **Old path held until:** <stability signal>

## Related

- Workflow: `../workflows/migration.md`. Hook: `../hooks/before-migration.md`. Skills: `../skills/database/backup-recovery`.
