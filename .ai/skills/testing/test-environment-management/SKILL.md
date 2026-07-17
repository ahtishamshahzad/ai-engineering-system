---
name: test-environment-management
description: Use to provision test environments — real production-engine datastores (containerized), faked externals, isolated per-run state, and critical environment/config validation so tests catch environment failures. Covers local, CI, and ephemeral E2E targets.
---

# Test Environment Management

## Purpose

Give tests environments that resemble production closely enough to catch real failures — production-engine datastores, faked externals, isolated state — and validate that critical environment/config is actually correct, so "works locally, breaks deployed" is caught in CI, not production.

## When to Use

- When setting up how integration/API/E2E tests get their runtime (DB, services, config).
- When establishing local vs CI vs ephemeral-E2E parity.
- **Not** for unit tests (no environment) or production infra (`../../devops/environment-management`).

## Inputs

- Datastore engine(s) from `../../database/database-selection`; external dependencies (`../../backend/third-party-integrations`).
- CI platform (`../../devops/ci-cd`) and E2E targets (`playwright-e2e`/`maestro-e2e`).

## Discovery Questions

- What real infrastructure must tests run against (same DB engine, cache, queue) vs fake?
- How is per-run isolation achieved (containers per suite, transaction rollback, ephemeral namespaces)?
- What environment/config must be **validated** so tests fail loudly on misconfiguration rather than passing falsely?

## Responsibilities

- Provision **production-engine datastores** for integration/API tests — containerized (testcontainers-style) or managed test instances — never a different engine (SQLite-for-Postgres masks bugs) (`integration-testing`, `api-integration-testing`).
- **Fake true externals** deterministically: provider stubs at your interfaces, email sinks, no live third-party calls or real sends (`../../backend/third-party-integrations` sandbox rules).
- Ensure **isolation per run/test**: fresh or transaction-isolated state, unique namespaces for parallel CI, teardown that leaves nothing behind (`test-data-management`).
- **Validate critical environment/config**: required env vars present and typed, DB reachable, migrations applied, secrets loaded — as a pre-test gate that fails clearly, so config errors surface as an explicit failure, not mysterious test breakage (`../../devops/environment-management`, `../../backend/backend-security`).
- Keep **local ↔ CI ↔ ephemeral-E2E parity**: the same provisioning path everywhere, differences in config only, so "passes in CI" means something locally too.
- For E2E: stand up a running app + backend against seeded state; tear down after (`playwright-e2e`/`maestro-e2e`).

## Required Workflow

1. Identify real-vs-faked dependencies for each test level.
2. Provision production-engine datastores + external fakes.
3. Wire per-run isolation + teardown.
4. Add the critical env/config validation pre-test gate.
5. Ensure local/CI/E2E parity; document the one provisioning path.

## Decision Rules

- Same datastore engine as production for anything touching the DB.
- Externals faked at your interfaces, deterministically — no live calls in tests.
- Config validation runs before tests and fails loudly — a false pass from missing config is worse than a clear setup failure.
- One provisioning path across environments; config-only differences.

## Rules

- No live third-party calls or real emails from any test environment.
- Isolation guarantees no cross-run/cross-test leakage.
- Critical env/config is validated, not assumed.

## Anti-Patterns

- SQLite in tests, Postgres in production.
- Shared long-lived test database accumulating state.
- Live external calls making tests flaky and non-hermetic.
- Tests passing green while config is broken (no validation gate).
- Local setup diverging from CI so "works on my machine" persists.

## Validation Checklist

- [ ] Production-engine datastores provisioned (containerized/managed).
- [ ] Externals faked deterministically; no live calls/sends.
- [ ] Per-run isolation + teardown wired.
- [ ] Critical env/config validation gate fails loudly.
- [ ] Local/CI/E2E parity via one provisioning path.

## Definition of Done

Test environments running production-engine datastores with faked externals, isolated per run, gated by critical env/config validation, and consistent across local/CI/E2E — so environment failures surface in tests, not production.

## Related Skills

`integration-testing`, `api-integration-testing`, `playwright-e2e`, `maestro-e2e`, `test-data-management`, `../../devops/environment-management`, `../../devops/ci-cd`, `../../backend/third-party-integrations`, `../../backend/backend-security`.

## Related Knowledge

`../../../knowledge/` (dependencies, infra parity needs).

## Related References

`../../../references/testing/` (provisioning patterns, when populated).

## Context Loading Guidance

- **Requires:** datastore engines, external deps, CI platform, E2E targets.
- **Does not require:** production infra config, unit-test setup.
- **May load:** `test-data-management`, `../../devops/environment-management`.
- **Stop when:** provisioning + isolation + config validation are wired with parity.

## Token Efficiency Guidance

The dependency table (real vs faked, isolation mechanism) plus the config-validation checklist carry the design.
