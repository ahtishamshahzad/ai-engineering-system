---
name: ownership-authorization
description: Use to design ownership, organization/tenant-scope, and object-level authorization — the caller may touch only their own or their tenant's resources. Scope derives from the session, never from client-supplied IDs; requires cross-user and cross-tenant negative tests.
---

# Ownership Authorization

## Purpose

Design the resource-bound authorization layers: **ownership** (this caller's resource), **organization/tenant scope** (inside the caller's org), and **object-level rules** (sharing, state-dependent access). These sit *after* role/permission checks and prevent IDOR/BOLA — the most common real-world authorization failure.

## When to Use

- Whenever resources belong to users or organizations, or have per-object access rules.
- **Not** for action-class gating — that's `role-permission-design`.

## Inputs

- Domain model: which entities have owners/orgs; sharing rules.
- Authorization matrix (`backend-authorization`); data layer (`../../database/`).

## Discovery Questions

- For each entity: owned by a user, scoped to an org/tenant, both, or global?
- Are resources ever shared (explicit grants, links, public flags)? State-dependent access (draft vs published)?
- Where does the caller's user/org context come from at request time (session/claims — never the payload)?
- On unauthorized access to an existing ID: 403, or 404 to hide existence?

## Responsibilities

- Map each entity to its scope model (owner / tenant / owner-within-tenant / shared / global-read).
- Enforce **in the query**: filter by owner/tenant from session context (`WHERE user_id = session.userId` / `org_id = session.orgId`) so unauthorized rows are never loaded — not fetch-then-check (which invites forgotten checks and leaks via error variance).
- Route derivation of scope from the **session only**: client-supplied `userId`/`orgId` in body/query/params are for locating *within* the caller's scope at most, never for widening it.
- Design object-level rules (grants tables, share links, status-based visibility) as explicit policy checks alongside the scope filter.
- Fix failure semantics (403 vs 404) per entity class with `backend-error-handling`.
- Require negative tests:
  - User A requests User B's resource ID → rejected (403/404 per policy).
  - Cross-tenant ID access → rejected, even with a valid guessed/enumerated ID.
  - Payload `userId`/`orgId` pointing elsewhere → ignored; scope stays the session's.
  - Shared-resource rules: non-grantee rejected, revoked grant rejected.

## Required Workflow

1. Build the entity × scope-model table.
2. Define query-level filters and where session context enters the data layer (e.g. repository helpers that require a scope).
3. Define object-level policies for shared/stateful access.
4. Fix 403-vs-404 semantics per entity.
5. Specify the negative-test suite; wire into `backend-integration-testing`.

## Decision Rules

- Scope in the query beats check-after-load; make unscoped queries on scoped entities impossible-by-default (scoped repository methods) where the data layer allows.
- Multi-tenant: **every** query on tenant data carries the tenant filter — including background jobs, exports, and admin tooling (admin cross-tenant access is its own audited permission, not a missing filter).
- Creation is scoped too: new resources take owner/tenant from the session, not the payload.
- Prefer 404 for existence-hiding on private resources; 403 where existence is already known (own-org objects without permission).

## Rules

- No endpoint or job touches scoped data without a scope derivation recorded.
- Client-supplied ownership IDs are never trusted (`backend-validation` validates shape; this skill removes trust).
- Every scoped entity ships with cross-user/cross-tenant negative tests.

## Anti-Patterns

- `findById(params.id)` with no owner/tenant filter (classic IDOR).
- Trusting `body.orgId` because "the client sends the right one."
- Scoping reads but not updates/deletes — or HTTP but not background jobs.
- Sequential integer IDs plus 403/404 variance leaking existence.
- Assuming role checks made ownership checks unnecessary.

## Validation Checklist

- [ ] Entity × scope-model table recorded.
- [ ] Scope enforced in queries from session context.
- [ ] Object-level/sharing policies explicit.
- [ ] Creation takes scope from session.
- [ ] 403/404 semantics fixed per entity.
- [ ] Negative tests: cross-user, cross-tenant, payload-ID-ignored, revoked-grant.

## Definition of Done

A recorded scope model per entity with query-level enforcement from session context, explicit object-level policies, fixed failure semantics, and a negative-test suite proving cross-user and cross-tenant access is rejected and client-supplied IDs are not trusted.

## Related Skills

`backend-authorization`, `role-permission-design`, `backend-validation`, `backend-error-handling`, `backend-integration-testing`, `../../database/database-security`, `../../security-review`.

## Related Knowledge

`../../../knowledge/` (tenancy model, sharing rules).

## Related References

`../../../references/backend/auth/` (scope-model table, when populated).

## Context Loading Guidance

- **Requires:** entity/ownership model, session context shape, authorization matrix.
- **Does not require:** role taxonomy detail, login flows.
- **May load:** `backend-authorization`, `../../database/database-security`.
- **Stop when:** scope models, enforcement points, and negative tests are recorded.

## Token Efficiency Guidance

The entity × scope table plus the negative-test list is the deliverable; keep enforcement description to where the filter lives, not repeated query code.
