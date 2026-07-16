# `.ai/skills/database/` — Database Skill Pack (Index)

Reusable, tool-neutral skills for choosing, designing, and operating databases (PostgreSQL, MySQL, MongoDB) and their data layers (Prisma, Drizzle, Mongoose, native driver). Discover them **through this index**, then load only the relevant ones (`../../system/SKILL_SELECTION_RULES.md`). **Do not load the whole pack** — most tasks need 1–3 skills.

Database skills keep two decisions separate — the **database** and the **data layer** — and pair them coherently (`../../stack-recommendation`). Relational stores are usually preferred for payments, orders, inventory, reporting, financial data, transactions, and strongly related entities; MongoDB fits genuinely variable documents with embedded access patterns. Constraints enforce invariants, migrations are reviewed code, and an untested backup doesn't count. Nothing is installed or scaffolded before the approval gates (`../../system/QUALITY_GATES.md`).

## Categories

### Selection & Modeling
| Skill | Description |
|-------|-------------|
| [`database-selection`](database-selection/SKILL.md) | Two separate decisions: database (PostgreSQL/MySQL/MongoDB) then data layer (Prisma/Drizzle/Mongoose/native); relational signals checked explicitly. |
| [`relational-schema-design`](relational-schema-design/SKILL.md) | Tables, keys, FK relationships, constraints-as-invariants, precise types (money!), justified denormalization. |
| [`document-schema-design`](document-schema-design/SKILL.md) | Embed vs reference per access pattern, growth bounds, owned duplication, validation despite "schemaless." |

### Data Layer
| Skill | Description |
|-------|-------------|
| [`prisma-relational`](prisma-relational/SKILL.md) | Prisma expression of the schema; reviewed migrations, select scope, N+1 plan, raw-SQL policy. |
| [`drizzle-relational`](drizzle-relational/SKILL.md) | Drizzle expression: constraints in-schema, explicit joins, generate→review→migrate, prepared statements. |
| [`mongoose-mongodb`](mongoose-mongodb/SKILL.md) | Strict validated schemas, populate discipline, deliberate index builds, thin hooks. |

### Change Management
| Skill | Description |
|-------|-------------|
| [`database-migrations`](database-migrations/SKILL.md) | Versioned immutable schema migrations; expand→migrate→contract; lock awareness; rollback ruling. |
| [`seed-data`](seed-data/SKILL.md) | Reference data (idempotent, prod-safe) vs dev fixtures (factories) vs test seeds — separated pipelines. |
| [`data-migration`](data-migration/SKILL.md) | Backfills/moves: profiled, batched, resumable, idempotent, verified, rehearsed, snapshot-backed. |

### Correctness & Performance
| Skill | Description |
|-------|-------------|
| [`indexing`](indexing/SKILL.md) | Indexes from query shapes: composite ordering, FK/tenant/unique coverage, plan-verified, write-cost aware. |
| [`transactions`](transactions/SKILL.md) | Multi-write invariants atomic at service boundaries; no external calls inside; outbox for side effects. |
| [`concurrency`](concurrency/SKILL.md) | Races: atomic updates, optimistic/pessimistic locking, constraints over check-then-act, idempotency keys. |
| [`database-performance`](database-performance/SKILL.md) | Evidence-ranked slow queries, EXPLAIN, N+1 elimination, pool sizing, lock diagnosis. |

### Safety & Operations
| Skill | Description |
|-------|-------------|
| [`database-security`](database-security/SKILL.md) | Least-privilege accounts, network isolation, parameterized-only, encryption per class, PII retention/deletion, tenant-isolation depth. |
| [`backup-recovery`](backup-recovery/SKILL.md) | RPO/RTO-driven backups + PITR, off-site copies, restore runbook, **rehearsed drills**. |

## Selection guidance (database)

| Situation | Load (only these) |
|---|---|
| New project persistence | `database-selection` → `relational-schema-design` or `document-schema-design` → one data-layer skill |
| Schema change | `database-migrations` (+ `data-migration` if backfill, + `indexing` if query shapes change) |
| New feature entities | the schema-design skill + the project's data-layer skill |
| Slow queries | `database-performance` → `indexing` (+ `../backend/backend-performance` for the app side) |
| Money/inventory/state correctness | `transactions` + `concurrency` |
| Environments & tests need data | `seed-data` |
| Moving/reshaping data | `data-migration` + `backup-recovery` (snapshot first) |
| Hardening / compliance | `database-security` → `../security-review` |
| Ops readiness | `backup-recovery` (+ `../release-planning`) |

## Dependency guidance (how database skills relate)

- **Selection first** (`database-selection`, two separate decisions) → one schema-design skill → one data-layer skill; everything else follows.
- **Schema design** pushes invariants into constraints; the data-layer skills *express* the design, never redesign it.
- **Change flows**: `database-migrations` owns DDL sequencing; `data-migration` owns the backfill leg between expand and contract; `seed-data` rides after migrations.
- **Correctness pair**: `transactions` (one operation atomic) + `concurrency` (operations colliding); both lean on constraints from schema design.
- **Performance pair**: `database-performance` finds offenders with evidence → `indexing` designs the fix; both verify with plans.
- **Safety**: `database-security` + `backup-recovery` gate release readiness with `../security-review` / `../release-planning`.
- **Backend coordination**: scope filters come from `../backend/ownership-authorization`; job/webhook idempotency pairs with `concurrency`; deploy ordering pairs with `../backend/backend-deployment`.

## References

Database reference material (examples, patterns — not permanent skill content) lives under:
`../../references/database/schema/`, `.../prisma/`, `.../drizzle/`, `.../mongoose/`, `.../migrations/`, `.../seeds/`, `.../performance/`, `.../transactions/`, `.../security/`, `.../operations/` — populated as the project develops.

## Rule: do NOT load the whole pack

Loading every database skill wastes context (`../../system/TOKEN_OPTIMIZATION_RULES.md`). Select the minimum relevant set via this index, load one stage at a time, and unload on task switch.
