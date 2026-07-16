---
name: backend-authorization
description: Use to plan backend authorization — who may do what. Distinguishes authentication, role authorization, permission authorization, ownership authorization, organization/tenant scope, and object-level authorization; enforces centrally per endpoint and requires negative tests.
---

# Backend Authorization

## Purpose

Design how the backend decides **who may do what** — as distinct, layered checks, each enforced server-side on every protected operation. This is the umbrella skill; `role-permission-design` and `ownership-authorization` detail the models.

## When to Use

- When any endpoint or operation must be restricted.
- **Not** for identity itself (`backend-authentication`) or abuse throttling (`rate-limiting`) — a rate limit is not an access decision.

## Inputs

- Auth session/claims design (`backend-authentication`).
- Endpoint inventory with sensitivity (`rest-api-design` / `graphql-api-design`).
- Domain model: owners, organizations/tenants, shared resources.

## Discovery Questions

- Which of these does the system actually need — and which does each endpoint need?
  1. **Authentication** — is the caller identified at all?
  2. **Role authorization** — does the caller's role permit this action class (admin vs user)?
  3. **Permission authorization** — does the caller hold the specific permission (finer than role)?
  4. **Ownership authorization** — does the caller own *this* resource?
  5. **Organization/tenant scope** — is the resource inside the caller's org/tenant?
  6. **Object-level authorization** — per-object rules beyond ownership (shared-with, state-dependent access)?
- Where is each check enforced (guard/middleware vs service vs query)?
- What happens on failure — 403, or 404 to hide existence?

## Responsibilities

- Classify every protected endpoint by which of the six levels apply — most non-trivial endpoints need authentication + one of role/permission + one of ownership/tenant/object.
- Place enforcement: coarse checks (authenticated, role/permission) at the edge (guard/middleware); resource-bound checks (ownership, tenant, object) in the service/query where the resource is loaded (`ownership-authorization`).
- Centralize decision logic (policy functions/guards) — no scattered `if (user.role === 'admin')` in handlers.
- Define failure behavior per resource class (403 vs existence-hiding 404) with `backend-error-handling`.
- Require **negative tests** for every protected operation:
  - User A cannot read/update/delete User B's resource.
  - Non-admin cannot invoke an admin action.
  - Client-supplied ownership/tenant IDs are ignored — scope derives from the session.
  - Cross-tenant access is rejected even with a valid, guessed ID.

## Required Workflow

1. Build the endpoint × authorization-level matrix.
2. Design the role/permission model (`role-permission-design`).
3. Design ownership/tenant/object checks (`ownership-authorization`).
4. Fix enforcement placement + failure responses.
5. Specify the negative-test suite; wire into `backend-integration-testing`.

## Decision Rules

- Every protected operation names its levels explicitly; "it's behind login" is level 1 only and usually insufficient.
- Role/permission checks never substitute for ownership/tenant checks — an authenticated user with the right role can still only touch *their* data unless the model says otherwise.
- Deny by default: an endpoint without a recorded authorization decision is a defect.
- UI hiding (client packs) is convenience; the server check is the boundary.

## Rules

- Authorization derives from server-side session/claims — never from client-supplied role, user ID, or org ID fields.
- One policy home per rule; duplicated checks drift.
- Every new endpoint lands with its negative tests, not after.

## Anti-Patterns

- Treating "authenticated" as "authorized."
- Role checks only, no ownership/tenant check on resource access (IDOR/BOLA).
- Authorization logic copy-pasted across handlers.
- Trusting `body.userId` / `body.orgId` for scoping.
- Confusing rate limiting or CAPTCHA with authorization.

## Validation Checklist

- [ ] Endpoint × level matrix recorded (all six levels considered).
- [ ] Enforcement placement fixed (edge vs service/query).
- [ ] Failure behavior (403 vs 404) decided per resource class.
- [ ] Policy logic centralized.
- [ ] Negative tests specified: cross-user, non-admin→admin, client-supplied IDs untrusted, cross-tenant rejected.

## Definition of Done

A recorded authorization design: per-endpoint level matrix, centralized enforcement with placement, failure behavior, and a negative-test suite covering cross-user, role-escalation, untrusted-ID, and cross-tenant cases.

## Related Skills

`backend-authentication`, `role-permission-design`, `ownership-authorization`, `backend-integration-testing`, `backend-error-handling`, `../../security-review`, `graphql-api-design` (resolver-level enforcement).

## Related Knowledge

`../../../knowledge/` (roles, tenancy model, sharing rules).

## Related References

`../../../references/backend/auth/` (authorization matrix, when populated).

## Context Loading Guidance

- **Requires:** session/claims design, endpoint inventory, tenancy/ownership model.
- **Does not require:** login flow internals, UI gating.
- **May load:** `role-permission-design`, `ownership-authorization`.
- **Stop when:** matrix, placement, and negative tests are recorded.

## Token Efficiency Guidance

The endpoint × level matrix is the deliverable — build it and let the two specialist skills carry model detail.
