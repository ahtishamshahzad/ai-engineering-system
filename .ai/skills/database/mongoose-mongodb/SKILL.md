---
name: mongoose-mongodb
description: Use to plan Mongoose over MongoDB — expressing the approved document design as schemas with real validation, indexes declared and built deliberately, lean queries, middleware discipline, and transactions only where the design demands them.
---

# Mongoose (MongoDB)

## Purpose

Express the approved document design (`document-schema-design`) through Mongoose: schemas that actually validate, deliberate indexes, efficient reads, and middleware that doesn't hide business logic.

## When to Use

- After MongoDB + Mongoose were approved (`database-selection`).
- **Not** for document design decisions (upstream) or relational projects.

## Inputs

- Approved document design: collection shapes, embed/reference map, duplication ledger, validation requirements.
- Hot query paths + index needs (`indexing`).

## Discovery Questions

- Which parts of each document are stable core (strict schema) vs the designed variable region (`Schema.Types.Mixed`/subdocument maps — scoped, not global)?
- Which queries are hot and read-only (lean candidates)?
- Any cross-document invariants needing sessions/transactions (`transactions`) — and are they rare enough to stay MongoDB-shaped?

## Responsibilities

- Define schemas mirroring the approved design: required fields, types, enums, match/min/max validators on the stable core; the variable region **explicitly scoped** — strict mode on, unknown paths rejected elsewhere.
- Model embed vs reference exactly as designed: subdocument schemas for embeds; `ObjectId` refs + deliberate `populate` policy for references (populate is a query per path — hot paths get explicit shaping or aggregation instead).
- Declare **indexes in the schema** to match `indexing`'s plan — but control build timing in production (autoIndex off; builds via migration/ops step — `database-migrations` for index rollouts on big collections).
- Set query discipline: `.lean()` for read-only paths (hydration costs), projections scoped to need, cursor pagination on stable keys, `maxTimeMS` on heavy queries.
- Keep **middleware (hooks) thin**: derived-field maintenance, timestamps — not business rules (those live in services, `../../backend/backend-api-architecture`); document every hook (hidden write amplification).
- Use **sessions/transactions** only for the flagged cross-document invariants; single-document atomicity is the default model.
- Enforce the duplication ledger: propagation updates implemented where the design assigned ownership.
- Version/migrate shapes deliberately: schema changes to live collections go through `data-migration` (backfills), not silent shape drift.

## Required Workflow

1. Translate collection designs into schemas; strict core + scoped variable regions.
2. Express embed/reference exactly per design; set populate/aggregation policy per hot path.
3. Declare indexes; plan production build strategy.
4. Set lean/projection/pagination/timeout conventions.
5. Implement duplication-propagation where owned; wire flagged transactions.
6. Verify validation rejects malformed documents (tests) and unknown fields.

## Decision Rules

- Strict mode stays on; `Mixed` appears only where the design named a variable region.
- `populate` chains on list endpoints are the Mongo N+1 — restructure (embed, aggregate, or batched fetch) when a hot path grows them.
- Validation in Mongoose complements collection-level JSON Schema (defense at the DB when the design requires it) — Mongoose-only validation vanishes for any non-Mongoose writer.
- If transactions become routine rather than exceptional, the domain may be relational — escalate to `database-selection`, don't normalize the pain.

## Rules

- No business logic in hooks; hooks documented.
- Index changes follow the `indexing` plan — no ad-hoc `index: true` sprinkling.
- Shape changes to existing collections ship with their backfill (`data-migration`).

## Anti-Patterns

- `strict: false` / Mixed-everywhere schemas ("flexible").
- Populate pyramids on hot list endpoints.
- autoIndex building indexes on production at boot.
- Hydrated full documents where `.lean()` + projection serves.
- Hooks that send emails or mutate other collections invisibly.
- Silent schema drift with no backfill — three shapes of the same collection in production.

## Validation Checklist

- [ ] Schemas mirror the design; strict core, scoped variable regions.
- [ ] Embed/reference + populate/aggregation policy per hot path.
- [ ] Indexes declared per plan; production build strategy set.
- [ ] lean/projection/pagination/timeout conventions recorded.
- [ ] Duplication propagation implemented per ledger.
- [ ] Transactions only where flagged; validation tests reject malformed/unknown.

## Definition of Done

The approved document design expressed as strict, validated Mongoose schemas with deliberate population/index/lean discipline, owned duplication propagation, and exceptional-only transactions — with malformed-document rejection proven by tests.

## Related Skills

`database-selection`, `document-schema-design`, `indexing`, `transactions`, `concurrency`, `data-migration`, `database-performance`, `database-security`, `seed-data`.

## Related Knowledge

`../../../knowledge/` (variable regions, duplication ledger).

## Related References

`../../../references/database/mongoose/` (patterns, when populated).

## Context Loading Guidance

- **Requires:** approved document design, hot paths, index plan.
- **Does not require:** re-deciding embed/reference, app feature code.
- **May load:** `indexing`, `data-migration` (shape changes).
- **Stop when:** schemas + conventions + propagation are recorded/implemented.

## Token Efficiency Guidance

Work per collection from the design table; show one schema exemplar, not all of them. The populate-policy list per hot path is the high-value artifact.
