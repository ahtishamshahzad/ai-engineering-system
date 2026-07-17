---
name: backend-integration-testing
description: Use to plan backend integration tests — real HTTP requests against the app with a real (containerized) database, covering auth flows, authorization negative tests (cross-user/cross-tenant/role), validation rejection, and webhook/job paths. External providers stay faked.
---

# Backend Integration Testing

## Purpose

Prove the assembled backend works: routes through middleware, validation, authorization, services, and a **real database** — exercised over HTTP the way clients will call it. This is where authorization and validation guarantees get *verified*, not just designed.

## When to Use

- For endpoint behavior, auth/authz enforcement, DB-touching flows, webhook intake, job execution.
- **Not** for pure logic (`backend-unit-testing`) or full multi-app E2E (client packs / `../../testing-strategy`).

## Inputs

- Endpoint design + auth matrix (`rest-api-design`, `backend-authorization`).
- Schema/migrations (`../../database/database-migrations`), seed strategy (`../../database/seed-data`).

## Discovery Questions

- How does a test get a real database — containerized per suite (testcontainers-style), shared with per-test isolation, or transaction-rollback per test?
- How do tests authenticate (helper that mints sessions/tokens per persona: anonymous, user A, user B, admin, other-tenant)?
- Which flows are release-critical and must be covered first?

## Responsibilities

- Stand up the **app + real database** in tests: migrations applied, known seed state, isolation between tests (truncate/transaction/unique-tenant per test) — SQLite-in-place-of-Postgres “convenience” swaps test a different database; don't.
- Fake only true externals: provider clients stubbed at your interface (`third-party-integrations`); queues either run inline/test-mode or assert enqueue + test workers directly (`background-jobs`).
- Cover per endpoint: success shape (contract match — `api-contracts`), validation rejection (malformed/unknown-field/oversize), and the **authorization negative suite**:
  - anonymous → 401 on protected routes;
  - **User A cannot read/update/delete User B's resource**;
  - **non-admin → admin action rejected**;
  - **client-supplied ownership/tenant IDs ignored** (scope stays the session's);
  - **cross-tenant access rejected** with a valid guessed ID.
- Cover auth flows end-to-end: signup/login/refresh/logout, expired/tampered tokens, reset-token single-use (`backend-authentication`).
- Cover non-HTTP entries: inbound webhooks (bad signature rejected, duplicate deduped — `webhooks`), job handlers (idempotent re-run — `background-jobs`).
- Keep the suite honest in CI: hermetic (no shared external state), parallelizable, failures reproducible locally.

## Required Workflow

1. Choose the DB provisioning + isolation strategy.
2. Build test helpers: app bootstrap, persona auth, seed factories.
3. Cover release-critical flows first; per endpoint add success + validation + authz-negative cases.
4. Add webhook/job-path tests.
5. Wire into CI as the merge gate; keep runtime bounded (parallel, scoped seeds).

## Decision Rules

- Same database engine as production, always.
- The authorization negative suite is **mandatory per scoped resource** — a missing cross-user test is a missing control.
- Assert response contracts (status + shape), not implementation artifacts.
- Flaky integration tests get fixed or quarantined same-day; a red-noise suite gates nothing.

## Rules

- No test hits real third-party services or sends real email (`third-party-integrations` sinks).
- Tests own their data; no dependence on execution order or leftover state.
- Every bug fix at this layer lands with its regression test.

## Anti-Patterns

- Mocking the data layer in an "integration" test — that's a slow unit test.
- Testing only happy paths; skipping the negative authz suite.
- One shared mutable seed database for the whole suite.
- Asserting exact error strings instead of codes/shapes.
- A 40-minute suite nobody runs locally.

## Validation Checklist

- [ ] Real same-engine DB, migrations + isolation strategy.
- [ ] Persona auth helpers (anonymous, A, B, admin, other-tenant).
- [ ] Per endpoint: success, validation rejection, authz negatives.
- [ ] Cross-user / non-admin / untrusted-ID / cross-tenant tests present.
- [ ] Webhook + job paths covered; externals faked.
- [ ] Suite hermetic, parallel, gating merges.

## Definition of Done

An integration suite running real HTTP against the app with a production-engine database — covering critical flows, validation rejection, and the full authorization negative suite — hermetic, CI-gating, and reproducible locally.

## Related Skills

`backend-unit-testing`, `../../testing-strategy`, `backend-authorization`, `ownership-authorization`, `backend-authentication`, `backend-validation`, `webhooks`, `background-jobs`, `../../database/database-migrations`, `../../database/seed-data`.

## Related Knowledge

`../../../knowledge/` (critical flows, personas/tenancy model).

## Related References

`../../../references/backend/testing/` (helper patterns, when populated).

## Context Loading Guidance

- **Requires:** endpoint + authz matrix, schema/seed strategy, CI constraints.
- **Does not require:** service internals (tests go over HTTP), provider docs.
- **May load:** `backend-authorization` (negative matrix), `../../database/seed-data`.
- **Stop when:** the suite plan (or tests) covering the matrix is done and CI-wired.

## Token Efficiency Guidance

Drive coverage from the endpoint × authz matrix — each cell is a test case; don't re-derive cases per endpoint in prose.
