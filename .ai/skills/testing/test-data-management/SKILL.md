---
name: test-data-management
description: Use to plan test data — factories over fixtures, per-test isolation, deterministic personas (users, roles, tenants) for authorization tests, and strict separation from production data. No real PII in tests; data is owned per test, not shared and mutated.
---

# Test Data Management

## Purpose

Give tests the data they need without the flakiness and risk that bad test data causes: factory-generated, per-test-owned, deterministic, and never sourced from production — so tests are isolated, repeatable, and safe.

## When to Use

- For any test needing data: integration, API, E2E, component with data.
- **Not** for reference/seed data the app requires (`../../database/seed-data`) — though tests reuse its factories.

## Inputs

- Schema + data layer (factories speak its language — `../../database/prisma-relational`/etc.).
- Personas needed for authorization tests (users, roles, tenants — `../../backend/backend-authorization`).
- Isolation strategy from the environment (`test-environment-management`).

## Discovery Questions

- What entities/personas do tests need (anonymous, user A, user B, admin, other-tenant)?
- How is data isolated per test (transaction rollback, truncate, unique-tenant-per-test)?
- Does any test path risk touching or copying production data (it must not)?

## Responsibilities

- Build **factories** (not static fixtures): parameterized builders producing valid entities with sensible defaults and per-test overrides; composable for relationships. Factories survive schema change; static dumps rot.
- Provide **deterministic personas** for authorization coverage: A/B users, roles, and separate tenants — the fixtures that make cross-user/cross-tenant denial tests possible (`api-integration-testing`, `integration-testing`).
- Enforce **per-test ownership + isolation**: each test creates what it needs and rolls back/cleans up; no shared mutable dataset that couples tests and causes order-dependent flakes (`flaky-test-audit`).
- Keep data **deterministic**: seeded randomness where variety is needed, fixed values where assertions depend on them; controlled clock for time-sensitive data.
- **Never use real production data or PII** in tests — generated fake data only; production dumps in test/dev are a breach vector (`../../security/privacy-review`, `../../database/database-security`).

## Required Workflow

1. Inventory needed entities + personas (incl. authorization actors).
2. Build/extend factories with defaults + overrides + relationships.
3. Wire per-test isolation with the environment strategy.
4. Provide the persona set for authorization tests.
5. Verify no production-data path; verify repeat-run determinism.

## Decision Rules

- Factories over fixtures — the default; static fixtures only for genuinely fixed reference values.
- Each test owns its data; shared mutable state is the top cause of flaky, order-dependent suites.
- Determinism where assertions depend on values; randomness (seeded) where variety exposes bugs.
- Zero real PII in tests — non-negotiable.

## Rules

- No production data or real PII in any test.
- Tests don't depend on each other's data or order.
- Factories stay typed against the current schema.

## Anti-Patterns

- Giant shared seed dataset every test reads and some mutate.
- Anonymized-ish production dumps as test data.
- Hardcoded IDs assuming a pre-existing row.
- Non-deterministic data breaking assertions intermittently.
- No distinct personas → authorization denial can't be tested.

## Validation Checklist

- [ ] Factories with defaults/overrides/relationships.
- [ ] Deterministic personas (A/B/admin/other-tenant) available.
- [ ] Per-test ownership + isolation wired.
- [ ] Determinism verified (repeat runs stable).
- [ ] No production data / PII anywhere.

## Definition of Done

Factory-driven, per-test-owned, deterministic test data with the personas needed for authorization coverage, isolated to prevent order dependence, and provably free of production data or PII.

## Related Skills

`../../database/seed-data`, `integration-testing`, `api-integration-testing`, `test-environment-management`, `flaky-test-audit`, `../../backend/backend-authorization`, `../../security/privacy-review`, `../../database/database-security`.

## Related Knowledge

`../../../knowledge/` (personas, entity shapes).

## Related References

`../../../references/testing/` (factory patterns, when populated).

## Context Loading Guidance

- **Requires:** schema/data layer, persona needs, isolation strategy.
- **Does not require:** production data (forbidden), full app source.
- **May load:** `test-environment-management`, `../../database/seed-data`.
- **Stop when:** factories + personas + isolation are in place and PII-free.

## Token Efficiency Guidance

The entity/persona inventory + factory list is the artifact; build per entity, don't dump example rows en masse.
