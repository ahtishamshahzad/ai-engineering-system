---
name: role-permission-design
description: Use to design the role and permission model — role set, permission granularity, role→permission mapping, storage/claims, admin surfaces, and evolution. Roles gate action classes; ownership/tenant checks remain separate.
---

# Role & Permission Design

## Purpose

Design the **role authorization** and **permission authorization** layers: which roles exist, which permissions they grant, how they're stored and checked, and how the model evolves without a rewrite.

## When to Use

- When the system has more than one class of user (user/admin, staff tiers, plan tiers).
- **Not** for per-resource access — ownership/tenant/object checks are `ownership-authorization`, layered on top of roles.

## Inputs

- Authorization matrix (`backend-authorization`), user classes from requirements.
- Session/claims mechanism (`backend-authentication`).

## Discovery Questions

- What action classes exist, and which user classes may perform each?
- Are plain roles enough, or do features need individual permissions (role = permission bundle)?
- Can one user hold multiple roles? Do roles vary per organization (tenant-scoped roles)?
- Who assigns roles, and is assignment itself an admin-only, audited action?

## Responsibilities

- Derive the **minimal role set** from real action classes — not speculative hierarchies.
- Decide granularity: role checks alone (small systems) vs **permission checks with roles as bundles** (checks stay stable while bundles evolve — prefer this once actions multiply).
- Define storage: roles/permissions in the database as data; claims in session/token as a cache of that data (with a staleness/refresh story if cached).
- Specify the check API: one central `can(user, action)` / guard — call sites never string-compare roles ad hoc.
- Design role assignment/removal as audited admin actions; protect against self-escalation.
- In multi-tenant systems, scope roles per organization membership (a user can be admin of org A, member of org B) — coordinate with `ownership-authorization`.

## Required Workflow

1. List action classes; map user classes → allowed actions.
2. Choose role-only vs role+permission granularity; record why.
3. Define storage, claims, and staleness handling.
4. Define the central check API and wire it into guards/policies.
5. Specify negative tests: non-admin cannot perform each admin action; role change takes effect (and revocation propagates).

## Decision Rules

- Check **permissions** at call sites when granularity is needed; roles remain the assignment unit.
- Never encode business rules in role names ("premium_user_v2") — map plans/features to permissions.
- Role claims cached in a JWT need a refresh/invalidation story; demotion must actually demote (`backend-authentication` lifecycle).
- Default role for new users is the least-privileged one.

## Rules

- Server-side data is the source of truth; client-asserted roles are never read.
- Role assignment is itself permission-gated and audited.
- Every admin surface/endpoint appears in the authorization matrix with its role/permission requirement.

## Anti-Patterns

- Hardcoded `role === 'admin'` string checks scattered through handlers.
- A role explosion (per-feature roles) instead of permissions.
- Roles in the JWT with no expiry/refresh, making demotion cosmetic.
- Assuming role checks cover per-resource access (they don't — IDOR remains).
- Letting users set their own role at signup via mass assignment (`backend-validation`).

## Validation Checklist

- [ ] Role set derived from real action classes.
- [ ] Granularity decision (role vs permission) recorded.
- [ ] Storage + claims + staleness handling defined.
- [ ] Central check API defined; no ad-hoc string checks.
- [ ] Assignment audited and escalation-protected.
- [ ] Negative tests: each admin action rejected for non-admins; demotion effective.

## Definition of Done

A recorded role/permission model — role set, granularity rationale, storage/claims, central check API, audited assignment — with negative tests proving non-admins are rejected and demotion propagates.

## Related Skills

`backend-authorization`, `ownership-authorization`, `backend-authentication`, `backend-validation`, `backend-integration-testing`, `../../security-review`.

## Related Knowledge

`../../../knowledge/` (user classes, plan/feature mapping).

## Related References

`../../../references/backend/auth/` (role matrix, when populated).

## Context Loading Guidance

- **Requires:** action classes, user classes, claims mechanism.
- **Does not require:** resource ownership model (separate skill), UI role gating.
- **May load:** `backend-authorization` (matrix), `ownership-authorization` (tenant-scoped roles).
- **Stop when:** the model + check API + negative tests are recorded.

## Token Efficiency Guidance

The role/permission × action table is the artifact; keep prose to the granularity and staleness decisions.
