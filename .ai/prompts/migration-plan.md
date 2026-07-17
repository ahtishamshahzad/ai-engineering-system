# Prompt: Migration Plan

> Tool-neutral starter. Plan an incremental, reversible migration.

---

Plan a **migration** (`.ai/` canonical; `.ai/workflows/migration.md`, `.ai/skills/migration-planning`, `.ai/skills/database/data-migration`).

**Migrating:** <source → target> · **Type:** schema | data | framework | infra

Do this:
1. Profile the real data; write the transformation spec with a ruling for every dirty-data case → `.ai/templates/MIGRATION.md`.
2. Plan a **batched, resumable, idempotent** runner; sequence with schema (expand → migrate → contract) for zero-downtime.
3. Require a **backup + tested restore** before any mutation; define pre-agreed verification criteria and a rollback ruling (`.ai/skills/database/backup-recovery`).
4. Rehearse at production scale before production.

Schema/migration files are owned by a single database engineer (no concurrent editors). Run `.ai/hooks/before-migration.md`. Present the plan before executing.
