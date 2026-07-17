---
name: authorization-security
description: Use to review authorization for security holes — IDOR/BOLA, missing ownership/tenant checks, privilege escalation, trusting client-supplied IDs/roles, and function-level access gaps — verified with negative tests. The security-review lens; backend-authorization is the design/build skill.
---

# Authorization Security

## Purpose

Find the access-control holes that dominate real breaches: broken object-level authorization (IDOR/BOLA), missing ownership/tenant scoping, privilege escalation, and trust of client-supplied identity — and prove they're closed with negative tests. The review lens; construction is `../../backend/backend-authorization` + `../../backend/ownership-authorization`.

## When to Use

- Reviewing authorization design or implementation for security.
- As the access-control slice of `../../security-review` and `threat-modeling` elevation/disclosure threats.
- **Not** for building authorization (backend pack) or authentication (`authentication-security`).

## Inputs

- The authorization design/implementation + endpoint inventory (`../../backend/backend-authorization`, `../../backend/ownership-authorization`).
- Tenancy/ownership model; threat model's access threats.

## Discovery Questions

- For each protected resource: is **ownership/tenant scope enforced in the query**, or fetched-then-maybe-checked (or not at all)?
- Are any **ownership/tenant IDs taken from the client** (body/query/params) and trusted for scoping?
- Can a **non-admin reach admin functions** (function-level access), or a user escalate role?
- Are **all** access paths covered — HTTP, GraphQL resolvers, background jobs, exports, admin tools?

## Responsibilities

- Hunt **IDOR/BOLA**: verify User A cannot access User B's resource by ID at every object-level endpoint; scope must be enforced in the query from session context, not checked after load or skipped (`../../backend/ownership-authorization`).
- Verify **no client-supplied ID is trusted for scope**: `body.userId`/`orgId` never widen access; scope derives from the session.
- Check **tenant isolation** everywhere: every query on tenant data carries the tenant filter — including jobs, exports, and admin tooling (cross-tenant access is its own audited permission, not a missing filter).
- Check **function-level authorization**: admin/privileged actions reject non-privileged callers; no privilege escalation via role mutation or mass assignment (`../../backend/role-permission-design`).
- Confirm **coverage across paths**: resolvers (`../../backend/graphql-api-design`), jobs, webhooks — not just REST happy paths.
- Require the **negative-test suite** as proof (`../../backend/backend-integration-testing`, `security-regression-testing`): cross-user, non-admin→admin, client-supplied-ID-ignored, cross-tenant-rejected.

## Required Workflow

1. Map protected resources/actions to their required scope checks.
2. Verify query-level ownership/tenant enforcement (not fetch-then-check).
3. Test client-supplied ID/role trust (attempt to widen scope).
4. Check function-level access + escalation paths.
5. Confirm coverage across all access paths.
6. Record findings; require negative tests as the fix's proof.

## Decision Rules

- Missing ownership/tenant check on an object endpoint = confirmed high-severity (IDOR/BOLA) — the most common real breach.
- Role checks do **not** substitute for ownership checks — an authorized role still only touches its own/tenant data unless the model says otherwise.
- Any scope derived from client input is a finding until proven ignored.
- A protected path without a negative test is unverified — treat as a gap.

## Rules

- Findings separated Confirmed vs Potential; never declare authorization "secure" (`../../security-review`).
- Every finding's fix is proven by a negative test.
- Coverage includes non-HTTP paths (jobs, exports, admin).

## Anti-Patterns

- Passing an endpoint because it "requires login" (authentication ≠ authorization).
- Role-only checks with no per-resource ownership/tenant check.
- Trusting `body.orgId`/`userId` for scoping.
- Reviewing REST but ignoring resolvers/jobs/exports.
- Accepting a fix with no cross-user/cross-tenant negative test.

## Validation Checklist

- [ ] Ownership/tenant enforced in-query at every object endpoint.
- [ ] No client-supplied ID/role trusted for scope.
- [ ] Tenant filter on all paths incl. jobs/exports/admin.
- [ ] Function-level access + escalation checked.
- [ ] Coverage across REST/GraphQL/jobs/webhooks.
- [ ] Negative-test suite proves each fix.

## Definition of Done

A recorded authorization security assessment — IDOR/BOLA, ownership/tenant scoping, escalation, client-ID trust, and cross-path coverage — with Confirmed/Potential findings each proven closed by a negative test, and no "secure" claim.

## Related Skills

`../../backend/backend-authorization`, `../../backend/ownership-authorization`, `../../backend/role-permission-design`, `../../backend/backend-integration-testing`, `security-regression-testing`, `api-security`, `../../security-review`, `threat-modeling`, `database-security`.

## Related Knowledge

`../../../knowledge/` (tenancy model, sharing rules, access threats).

## Related References

`../../../references/security/` (authorization review checklists, when populated).

## Context Loading Guidance

- **Requires:** authorization design/impl, endpoint inventory, tenancy model.
- **Does not require:** authentication internals, unrelated code.
- **May load:** `../../backend/ownership-authorization`, `security-regression-testing`.
- **Stop when:** findings + negative-test-proven fixes are recorded.

## Token Efficiency Guidance

The endpoint × scope-check × negative-test matrix is the artifact; focus on object-level and tenant paths where breaches concentrate.
