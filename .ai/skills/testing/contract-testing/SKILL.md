---
name: contract-testing
description: Use to verify that independently deployed clients and services agree on their API contract — schema/consumer-driven checks in CI that fail when a provider change would break a consumer, catching integration breakage before deploy rather than in production.
---

# Contract Testing

## Purpose

Catch API-contract breakage **between separately deployed parts** (frontend↔backend, service↔service, you↔a partner) in CI — so a provider change that would break a consumer fails the build, not a customer.

## When to Use

- When client and server (or two services) deploy independently and must agree on request/response shapes.
- When a mobile client (slow to update) consumes an evolving API.
- **Not** for a single deployable where API integration tests already exercise both sides in one process.

## Inputs

- The contract source of truth (OpenAPI / GraphQL schema — `../../backend/api-contracts`).
- Consumers and their update cadence (mobile store cycles, partner systems).

## Discovery Questions

- What parts deploy independently and could drift out of sync?
- Is there a single contract source, or types hand-copied between repos (drift risk)?
- Which consumers lag furthest (mobile), setting the deprecation window?

## Responsibilities

- Anchor tests to the **contract source of truth**; verify the provider's actual responses conform to it, and that consumers only depend on what the contract promises.
- Choose the approach: **schema-conformance** (validate live/recorded responses against OpenAPI/GraphQL schema) for simpler setups; **consumer-driven contracts** (each consumer publishes expectations the provider verifies) when many consumers and independent teams justify the tooling.
- Run in **CI on both sides**: provider build fails if it breaks the agreed contract; drift between contract and implementation fails (`../../backend/api-contracts`).
- Enforce the **breaking-change policy**: additive changes pass; removals/renames/retypes/newly-required fields fail unless versioned with a deprecation window covering the slowest consumer.
- Complement, not replace, `api-integration-testing` (behavior) — contract tests check *shape agreement*, not business correctness.

## Required Workflow

1. Confirm the contract source of truth and its CI drift check.
2. Choose schema-conformance vs consumer-driven per team/consumer count.
3. Wire provider-side verification into CI (fail on incompatible change).
4. Encode the breaking-change policy + deprecation window.
5. Keep contract tests distinct from behavior tests.

## Decision Rules

- One source of truth — hand-synced types across repos are the drift this skill exists to kill.
- Schema-conformance is enough for one-team, few-consumer setups; reach for consumer-driven tooling only when the coordination cost is real.
- The slowest consumer sets the deprecation window (mobile release cadence).
- A breaking change without a version bump fails CI — that's the whole point.

## Rules

- Contract changes reviewed as part of code review (`../../code-review`).
- Generated/contract artifacts never hand-edited.
- Deprecations documented for consumers before removal.

## Anti-Patterns

- Relying on manual coordination ("tell the mobile team") instead of a failing test.
- Copy-pasted types between client and server repos.
- Treating contract tests as behavior tests (or vice versa).
- Silent breaking changes shipped because nothing checked the contract.
- Deprecation windows shorter than the mobile release cycle.

## Validation Checklist

- [ ] Contract source of truth confirmed; drift check in CI.
- [ ] Approach chosen (schema-conformance vs consumer-driven) with reason.
- [ ] Provider-side CI verification fails on incompatible change.
- [ ] Breaking-change policy + deprecation window encoded.
- [ ] Distinct from behavior tests.

## Definition of Done

Contract verification wired into CI on the provider side against a single source of truth — incompatible changes fail the build, additive ones pass, and the deprecation window covers the slowest consumer.

## Related Skills

`../../backend/api-contracts`, `api-integration-testing`, `../../backend/rest-api-design`, `../../backend/graphql-api-design`, `../../code-review`, `../../devops/ci-cd`.

## Related Knowledge

`../../../knowledge/` (consumers, release cadences).

## Related References

`../../../references/testing/` (contract patterns, when populated).

## Context Loading Guidance

- **Requires:** contract source, consumer list + cadences.
- **Does not require:** business-logic internals, full app source.
- **May load:** `../../backend/api-contracts`, `api-integration-testing`.
- **Stop when:** provider-side contract verification gates CI.

## Token Efficiency Guidance

Reference the contract file, not its contents; the compatibility policy table (change type → pass/fail) is the artifact.
