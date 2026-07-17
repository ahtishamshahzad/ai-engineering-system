---
name: seed-data
description: Use to plan seed data — reference data the app requires (idempotent, environment-aware), development fixtures via factories, deterministic test seeds, and the hard rule that production seeding is limited, reviewed reference data only.
---

# Seed Data

## Purpose

Define what data environments start with and how it gets there: required reference data (all environments, idempotent), rich development fixtures, deterministic test seeds — each class handled differently, never mixed.

## When to Use

- When establishing environments/tests that need starting data, or when reference data changes.
- **Not** for moving real data between systems (`data-migration`) or schema change (`database-migrations`).

## Inputs

- Schema/data layer (seeds speak its language — `prisma-relational`/`drizzle-relational`/`mongoose-mongodb`).
- Domain's reference data (roles, plans, statuses, country lists…) and test personas (`../backend/backend-integration-testing`).

## Discovery Questions

- What data must exist for the app to function at all (reference data) vs what's convenience (dev fixtures)?
- Which environments get which classes — and what may touch production (short answer: only reviewed reference data)?
- Do integration tests need factories with per-test isolation (`backend-integration-testing` strategy)?

## Responsibilities

- Separate the three classes and their pipelines:
  - **Reference data** (roles, permission definitions, plans, enum-like tables): required everywhere including production; **idempotent upserts** keyed on stable natural keys; versioned in the repo; changes reviewed like migrations (often *run* as data migrations — coordinate with `database-migrations` ordering).
  - **Development fixtures**: realistic volume and variety (enough rows to expose pagination/perf issues), personas for every role/tenant case (`../backend/ownership-authorization` testing needs), generated via **factories** with a fixed random seed for reproducibility; never runnable against production.
  - **Test seeds**: minimal, deterministic, per-test-owned via factories (`backend-integration-testing` isolation) — no shared mutable "test database dump."
- Make all seeding **idempotent and re-runnable**: upsert by natural key; running twice changes nothing.
- Enforce environment guards in the seed runner itself (refuses production unless running the reference-data set; no fixture path can reach prod credentials).
- Keep secrets and real PII out of seeds: generated fake data only; production dumps are not fixtures (`database-security` — real PII in dev is a breach vector).
- Wire seeds into workflows: fresh-environment bootstrap, CI test setup, local reset command.

## Required Workflow

1. Inventory reference data vs fixture needs vs test personas.
2. Implement reference seeds as idempotent upserts; order after migrations.
3. Build fixture factories (seeded randomness, realistic volumes, all personas).
4. Align test seeding with the integration-test isolation strategy.
5. Add environment guards; verify re-run safety (run twice, diff nothing).

## Decision Rules

- If the app breaks without it, it's reference data — it ships to production through review; everything else never does.
- Factories beat static dumps: they survive schema change and compose per test.
- Fixture volume is a design input: 3 rows hide every pagination and index problem; seed hundreds where lists matter.
- Seed code is real code — typed against the schema, updated with it, reviewed.

## Rules

- Idempotent, or it isn't a seed.
- No real customer data, credentials, or tokens in any seed.
- Production seeding = reference data only, reviewed, run through the deploy pipeline.

## Anti-Patterns

- One `seed.ts` mixing roles (required) with 50 fake users (fixtures) — and someone running it in prod.
- `create()` seeds that duplicate rows on every run.
- Anonymized-ish production dumps as dev data.
- Test suites depending on a magic shared seed state no one dares change.
- Seeds silently drifting from schema until CI's the only place they run.

## Validation Checklist

- [ ] Three classes separated with distinct pipelines.
- [ ] Reference seeds idempotent, versioned, production-safe, ordered after migrations.
- [ ] Fixture factories: seeded randomness, personas, realistic volumes.
- [ ] Test seeding matches integration isolation strategy.
- [ ] Environment guards in the runner; re-run verified no-op.
- [ ] No PII/secrets anywhere in seeds.

## Definition of Done

A recorded, implemented seed strategy — idempotent reviewed reference data through the pipeline, factory-driven fixtures and deterministic test seeds guarded away from production — verified re-runnable.

## Related Skills

`database-migrations`, `data-migration`, `../backend/backend-integration-testing`, `../backend/role-permission-design` (role/permission reference rows), `database-security`, `prisma-relational`, `drizzle-relational`, `mongoose-mongodb`.

## Related Knowledge

`../../../knowledge/` (reference-data inventory, personas).

## Related References

`../../../references/database/seeds/` (factory patterns, when populated).

## Context Loading Guidance

- **Requires:** schema, reference-data inventory, test personas, environment list.
- **Does not require:** application feature code, production data.
- **May load:** `../backend/backend-integration-testing` (isolation), `database-migrations` (ordering).
- **Stop when:** the three pipelines are implemented/recorded and re-run-safe.

## Token Efficiency Guidance

The class × environment matrix plus the reference-data inventory carry the design; factories are read per entity, not en masse.
