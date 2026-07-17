---
name: api-security
description: Use to review API security — input validation/injection, broken object/function-level authorization, mass assignment, resource exhaustion, information leakage in errors, and public-endpoint abuse. Aligns with the OWASP API risks; drives fixes and negative tests.
---

# API Security

## Purpose

Assess the API's exposed surface against the ways APIs actually get breached — the OWASP API Security Top-10 shape: broken object/function-level authorization, injection, mass assignment, unrestricted resource consumption, and information leakage — and drive fixes proven by tests.

## When to Use

- Reviewing a REST/GraphQL API for security, in `../../security-review`.
- **Not** for building the API (backend pack) or browser-specific concerns (`web-security`) — though they overlap.

## Inputs

- The API design/implementation + endpoint inventory (`../../backend/rest-api-design`, `../../backend/graphql-api-design`).
- Authorization and validation designs (`authorization-security`, `../../backend/backend-validation`).

## Discovery Questions

- Is every input validated server-side (body, params, query, headers, uploads) and are queries parameterized (no injection)?
- Is object- and function-level authorization enforced per endpoint (→ `authorization-security` owns the depth)?
- Can clients set fields they shouldn't (mass assignment) or exhaust resources (no pagination/limits/depth caps)?
- Do errors leak internals (stack traces, SQL, existence)?

## Responsibilities

- **Injection + validation**: confirm server-side schema validation at every entry point (`../../backend/backend-validation`) and parameterized data access (`database-security`); reject unknown fields.
- **Authorization** (delegate depth to `authorization-security`): confirm object-level (IDOR/BOLA) and function-level checks exist per endpoint and across resolvers/jobs.
- **Mass assignment**: clients can't set privileged fields (role, ownerId, isAdmin) via whitelisted-input gaps — validation strips unknown fields; sensitive fields set server-side only.
- **Resource consumption**: pagination enforced, body-size/array limits, GraphQL depth/cost limits, rate limits on expensive/public endpoints (`abuse-prevention`, `../../backend/rate-limiting`) — unrestricted consumption is a DoS + cost attack.
- **Information leakage**: errors return safe shapes (`../../backend/backend-error-handling`) — no stack traces, SQL, internal IDs, or existence oracles; verbose debug off in production.
- **Transport + headers**: TLS, correct CORS (no wildcard-with-credentials), security headers (`web-security` for browser-facing).
- Route findings to fixes + **security regression tests** (`security-regression-testing`).

## Required Workflow

1. Enumerate endpoints + their inputs and auth requirements.
2. Check validation/injection and (via `authorization-security`) object/function-level access.
3. Check mass-assignment and resource-consumption controls.
4. Check error/information leakage + transport/CORS/headers.
5. Record findings (Confirmed/Potential); route to fixes + regression tests.

## Decision Rules

- Broken object-level authorization and injection are the highest-impact API findings — verify them first.
- Unknown-field stripping is the mass-assignment defense; "the client won't send that" is not.
- Every collection/expensive endpoint needs limits — unbounded is a finding.
- Errors reveal nothing internal — a stack trace in a response is a confirmed finding.

## Rules

- Findings separated Confirmed vs Potential; never declare the API "secure" (`../../security-review`).
- Each finding maps to a fix + a negative/regression test.
- Depth of authorization review delegated to `authorization-security`; don't duplicate.

## Anti-Patterns

- Reviewing validation but skipping object-level authorization (IDOR).
- Trusting client input for privileged fields.
- Endpoints with no pagination/size/depth limits.
- Stack traces / SQL / existence leaks in error responses.
- `cors({ origin: true, credentials: true })`.

## Validation Checklist

- [ ] Server-side validation + parameterized access at every entry point.
- [ ] Object + function-level authorization verified (via `authorization-security`).
- [ ] Mass assignment blocked (unknown fields stripped; sensitive fields server-set).
- [ ] Resource-consumption limits (pagination, size, depth, rate).
- [ ] No information leakage in errors; TLS/CORS/headers correct.
- [ ] Findings → fixes → regression tests.

## Definition of Done

A recorded API security assessment across the OWASP API risk shape — validation/injection, object/function-level authorization, mass assignment, resource consumption, information leakage, transport — with Confirmed/Potential findings routed to fixes and regression tests, and no "secure" claim.

## Related Skills

`authorization-security`, `../../backend/backend-validation`, `../../backend/backend-error-handling`, `../../backend/rate-limiting`, `abuse-prevention`, `web-security`, `database-security`, `security-regression-testing`, `../../security-review`, `threat-modeling`.

## Related Knowledge

`../../../knowledge/` (endpoint inventory, API threats).

## Related References

`../../../references/security/` (API review checklists, when populated).

## Context Loading Guidance

- **Requires:** API design/impl, endpoint inventory, validation/authz designs.
- **Does not require:** unrelated app internals, browser-only concerns beyond headers/CORS.
- **May load:** `authorization-security`, `abuse-prevention`.
- **Stop when:** findings + routed fixes/tests are recorded.

## Token Efficiency Guidance

The endpoint × risk-category matrix is the artifact; delegate authorization depth to `authorization-security` rather than re-deriving it.
