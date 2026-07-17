---
name: api-contracts
description: Use to define and maintain the API contract between backend and clients — OpenAPI/GraphQL schema as source of truth, shared/generated types, breaking-change policy, and contract-first workflow for new endpoints.
---

# API Contracts

## Purpose

Make the API contract explicit and enforceable: one source of truth (OpenAPI spec or GraphQL schema), types shared or generated from it, and a policy for what may change without breaking clients.

## When to Use

- When backend and any client (web/mobile/third party) must agree on shapes.
- When adding/changing endpoints — contract first, then implementation.
- **Not** for internal service-to-service shapes with a single owner (still useful, lower ceremony).

## Inputs

- Endpoint/schema design (`rest-api-design` / `graphql-api-design`).
- Client list and how they consume types (codegen, shared package, manual).

## Discovery Questions

- Where does the contract live, and what generates from it (types, clients, docs)?
- Which clients are deployed independently and can lag the backend?
- Is the contract written first, or inferred from code — and is that enforced?

## Responsibilities

- Establish the **source of truth**: OpenAPI document or GraphQL SDL, versioned in the repo.
- Plan **type flow**: generate client types/servers stubs from the contract, or generate the contract from typed server code — one direction, enforced in CI.
- Define the **breaking-change policy**: additive (new optional fields, new endpoints) is safe; removing/renaming/retyping/making-required breaks — requires a version bump or deprecation window.
- Keep validation schemas (`backend-validation`) and the contract from drifting — ideally derived from the same definitions.
- Document error shapes and auth requirements in the contract, not just prose.

## Required Workflow

1. Choose contract format + storage location.
2. Choose the generation direction and wire it into CI (drift fails the build).
3. Record the breaking-change policy and deprecation process.
4. For each new/changed endpoint: update contract → review → implement → verify against contract.

## Decision Rules

- One source of truth; hand-maintained parallel type definitions are drift waiting to happen.
- Mobile clients lag: deprecation windows must cover store-release cycles (`../../mobile/` pack, if present).
- Contract review is part of code review for any endpoint change (`../../code-review`).

## Rules

- No endpoint ships that isn't in the contract.
- Breaking changes require explicit approval and a migration note for clients.
- Generated artifacts are never hand-edited.

## Anti-Patterns

- Types copy-pasted between backend and frontend repos.
- Contract updated after implementation "when there's time."
- Silent breaking changes ("just renamed a field").
- OpenAPI docs that describe an older API than the deployed one.

## Validation Checklist

- [ ] Source of truth chosen and versioned.
- [ ] Generation direction wired; drift detected in CI.
- [ ] Breaking-change policy recorded.
- [ ] Error shapes + auth requirements in the contract.
- [ ] Deprecation window covers slowest client.

## Definition of Done

A versioned contract source of truth with enforced type flow, a recorded breaking-change policy, and a contract-first workflow for endpoint changes.

## Related Skills

`rest-api-design`, `graphql-api-design`, `backend-validation`, `backend-error-handling`, `../../code-review`, `../../documentation`.

## Related Knowledge

`../../../knowledge/` (client release cadences, integration owners).

## Related References

`../../../references/backend/api/` (contract conventions, when populated).

## Context Loading Guidance

- **Requires:** endpoint design, client list.
- **Does not require:** handler implementations, database schema.
- **May load:** `backend-validation` (schema sharing).
- **Stop when:** contract source, type flow, and change policy are recorded.

## Token Efficiency Guidance

Reference the contract file instead of pasting it; review diffs of the contract, not the whole document.
