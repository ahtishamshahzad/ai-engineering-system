---
name: security-regression-testing
description: Use to turn security findings and threats into automated tests that stay green — negative tests for authorization/injection/abuse, a test per fixed vulnerability, and CI enforcement so security regressions can't silently return. Security's regression discipline.
---

# Security Regression Testing

## Purpose

Make security durable: every fixed vulnerability and every important threat becomes an **automated test** that fails if the protection is removed — so a refactor or new feature can't silently reopen a closed hole. The security counterpart to `../../testing/regression-testing`.

## When to Use

- After any security fix, and to encode threat-model mitigations as tests.
- As the enforcement arm of the security skills and `../../security-review`.
- **Not** as a substitute for the review itself — it locks in what the review found.

## Inputs

- Security findings + fixes (from the security skills), threat-model mitigations (`threat-modeling`).
- Test infrastructure (`../../testing/api-integration-testing`, `../../testing/integration-testing`) with personas (`../../testing/test-data-management`).

## Discovery Questions

- For each fixed vulnerability: what test **fails on the vulnerable code** and passes once fixed?
- Which threats are testable as **negative tests** (access denied, input rejected, abuse throttled)?
- Are the personas (user A/B, admin, other-tenant) available to express authorization negatives (`../../testing/test-data-management`)?

## Responsibilities

- Write a **test per fixed vulnerability**: reproduces the exploit against the vulnerable code (fails), passes after the fix — at the lowest level that captures it (`../../testing/regression-testing` discipline, security-focused).
- Encode **negative security tests** for the standing threats:
  - **authorization**: cross-user, non-admin→admin, client-supplied-ID-ignored, cross-tenant-rejected (`authorization-security`, `../../backend/backend-integration-testing`);
  - **injection/validation**: malicious inputs (SQLi/NoSQL-operator/XSS payloads) rejected/encoded (`api-security`, `web-security`);
  - **abuse**: limits trip, CAPTCHA required on the right flows (`abuse-prevention`);
  - **auth**: tampered/expired/reused tokens rejected, enumeration resisted (`authentication-security`).
- **Enforce in CI**: these tests run on every change (`../../devops/ci-cd`) — a security test that isn't run guards nothing.
- Keep tests **behavioral** so they survive refactors and still catch reintroduction.
- Feed **incident postmortems** back into tests (`../../devops/incident-readiness`) — every security incident leaves a regression test.

## Required Workflow

1. For each finding/mitigation, define the failing-first test + its level.
2. Confirm each fails on vulnerable code, passes after fix.
3. Add the standing negative-test suites (authz/injection/abuse/auth).
4. Wire all into the CI-gated suite.
5. Feed incident learnings back as new tests.

## Decision Rules

- A security fix without a test invites its return — the test is part of the fix.
- Failing-first: a security regression test that passed before the fix proves nothing.
- Lowest capturing level (unit/integration/API) for speed and stability.
- Behavioral assertions over brittle snapshots, so refactors don't erase the guard.

## Rules

- Every security fix lands with a failing-first regression test.
- Negative security tests run in CI, not on demand.
- Postmortem findings become tests.

## Anti-Patterns

- Fixing a vulnerability with no test locking it closed.
- Security tests that never failed (prove nothing).
- Authorization "covered" by happy-path tests with no denial cases.
- A security suite that exists but isn't in CI.
- Incidents resolved with no regression test, inviting recurrence.

## Validation Checklist

- [ ] A failing-first test per fixed vulnerability, at the right level.
- [ ] Standing negatives: authz, injection, abuse, auth.
- [ ] Behavioral assertions (refactor-durable).
- [ ] All wired into CI-gated suites.
- [ ] Postmortem findings encoded as tests.

## Definition of Done

Every fixed vulnerability and key threat guarded by a failing-first, behavioral security test — authorization, injection, abuse, and auth negatives included — running in CI so security regressions cannot silently return, with incidents feeding new tests.

## Related Skills

`../../testing/regression-testing`, `authorization-security`, `authentication-security`, `api-security`, `web-security`, `mobile-security`, `abuse-prevention`, `threat-modeling`, `../../testing/api-integration-testing`, `../../testing/test-data-management`, `../../devops/ci-cd`, `../../devops/incident-readiness`, `../../security-review`.

## Related Knowledge

`../../../knowledge/` (past vulnerabilities, threat mitigations).

## Related References

`../../../references/security/` (security test patterns, when populated).

## Context Loading Guidance

- **Requires:** the findings/mitigations to encode, test infra + personas.
- **Does not require:** re-running the reviews, unrelated app code.
- **May load:** `../../testing/api-integration-testing`, `authorization-security`.
- **Stop when:** failing-first tests + standing negatives are CI-gated.

## Token Efficiency Guidance

The finding/threat → test table (level, failing-first, CI) is the artifact; one test per finding at the right level.
