# Checklist: Authorization

> Verifiable items for authorization (who may do what). Distinct from authentication and from abuse prevention.

## Pass Criteria

- [ ] Each protected operation names its levels: authN · role · permission · **ownership** · org/tenant scope · object-level (`../skills/backend/backend-authorization`).
- [ ] Scope enforced **in the query** from session context — unauthorized rows never loaded (not fetch-then-check) (`../skills/backend/ownership-authorization`).
- [ ] **Client-supplied ownership/tenant IDs are never trusted** for scope; scope derives from the session.
- [ ] Tenant filter applied on **every** path including background jobs, exports, and admin tooling.
- [ ] Function-level access: non-admin cannot invoke admin actions; no privilege escalation via role mutation/mass assignment.
- [ ] Failure behavior decided per resource (403 vs existence-hiding 404); policy logic centralized (no scattered role string checks).
- [ ] **Negative tests present:** User A ↛ User B's resource; non-admin ↛ admin action; client-supplied IDs ignored; cross-tenant rejected.

## Fail / Stop

- "Authenticated" treated as "authorized"; role-only checks with no ownership/tenant check; trusting `body.orgId`; missing negative tests.

## Related

Skills: `../skills/backend/backend-authorization`, `ownership-authorization`, `../skills/security/authorization-security`. Reference: `../references/backend/authorization/`.
