---
name: backend-unit-testing
description: Use to plan backend unit tests — services and pure business logic tested in isolation with focused test doubles, fast and deterministic, covering edge and failure paths. Route/database/HTTP coverage belongs to integration testing.
---

# Backend Unit Testing

## Purpose

Test business logic in isolation: services, domain rules, mappers, policy functions — fast, deterministic, and independent of HTTP, databases, and providers. What crosses process boundaries is `backend-integration-testing`'s job.

## When to Use

- For logic-bearing units: services, calculators, policies (`backend-authorization` decisions as functions), validators' custom rules, error mapping.
- **Not** for route wiring, real queries, or provider calls — integration territory.

## Inputs

- Testing strategy (`../../testing-strategy`) and the layer map (`backend-api-architecture` — services are the prime target).
- Framework/test-runner conventions already in the repo.

## Discovery Questions

- Which modules hold real logic (vs pass-through plumbing not worth unit tests)?
- Are services injectable/constructible with test doubles (DI or explicit deps)?
- What time/randomness/ID generation needs controlling for determinism?

## Responsibilities

- Target the **service layer and pure logic**; skip trivial pass-throughs — a unit test of a one-line delegation tests nothing.
- Isolate with **focused doubles**: stub the data-layer interface and provider interfaces (`third-party-integrations` isolation pays off here); don't mock what you own trivially — refactor for constructibility instead.
- Cover the paths that matter: happy path, edge cases (bounds, empty, duplicates), and **failure paths** (dependency throws, policy denies) — error behavior is logic.
- Keep tests deterministic: fake clock, seeded/injected IDs, no real network/filesystem/DB, no order dependence.
- Make tests document behavior: names state the rule ("expired token is rejected"), assertions check outcomes, not implementation internals.
- Wire into CI as the fast tier — the suite runs in seconds and gates every change (`../../git-workflow` hooks).

## Required Workflow

1. Identify logic-bearing units from the layer map.
2. Ensure constructibility with test doubles (flag DI problems to the foundation skill).
3. Write tests per unit: happy, edge, failure — regression test for every bug fixed (`../../bug-investigation`).
4. Verify determinism (repeat runs, no shared state).
5. Keep the suite in the fast CI tier.

## Decision Rules

- Mock at **boundaries you own as interfaces** (repo, provider client), not deep internals — over-mocked tests pin implementation and rot on refactor.
- A test that duplicates the implementation's arithmetic proves nothing; assert known input→output pairs.
- Coverage % is a smell detector, not a target — uncovered *logic* matters, uncovered plumbing doesn't.
- Authorization policies get unit tests per rule here **and** endpoint-level negative tests in integration — both, not either.

## Rules

- No network, no real DB, no real clock in unit tests.
- One behavior per test; failures must point at the rule that broke.
- Bug fixes land with the failing-then-passing test.

## Anti-Patterns

- Unit-testing controllers by mocking the entire framework.
- Mock setups longer than the logic under test.
- Asserting internal call sequences instead of outcomes.
- Snapshot tests as a substitute for behavioral assertions.
- Skipping failure-path tests ("it usually works").

## Validation Checklist

- [ ] Logic-bearing units identified; plumbing consciously skipped.
- [ ] Doubles at owned boundaries only.
- [ ] Happy + edge + failure paths covered.
- [ ] Deterministic (clock/ID/network controlled).
- [ ] Suite fast, in CI, gating changes.

## Definition of Done

A fast, deterministic unit suite covering the service layer's real logic — edges and failures included — isolated at owned boundaries, gating changes in CI.

## Related Skills

`../../testing-strategy`, `backend-integration-testing`, `backend-api-architecture`, `backend-authorization` (policy rules), `../../bug-investigation`, `../../code-review`.

## Related Knowledge

`../../../knowledge/` (domain rules worth encoding as tests).

## Related References

`../../../references/backend/testing/` (conventions, when populated).

## Context Loading Guidance

- **Requires:** layer map, the unit under test + its interface deps.
- **Does not require:** route definitions, migrations, provider docs.
- **May load:** `backend-integration-testing` (boundary split), `../../testing-strategy`.
- **Stop when:** the unit-test plan (or the tests) for the target module is done.

## Token Efficiency Guidance

Read the unit and its interfaces, not the whole app. Table of case → expected outcome per unit beats prose test plans.
