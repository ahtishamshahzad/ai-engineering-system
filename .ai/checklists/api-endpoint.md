# Checklist: API Endpoint

> Verifiable items for adding/changing a backend API endpoint.

## Pass Criteria

- [ ] Follows REST/GraphQL conventions (naming, methods, status codes) and the API contract (`../skills/backend/rest-api-design`, `api-contracts`).
- [ ] **Input validated server-side** at every entry point; unknown fields stripped (`../skills/backend/backend-validation`).
- [ ] **Authorization** enforced: authN + the right level (role/permission/**ownership/tenant**); scope from the session, not client-supplied IDs (`../skills/backend/backend-authorization`, `ownership-authorization`).
- [ ] Errors use the central safe shape; no stack traces / SQL / internals leaked (`../skills/backend/backend-error-handling`).
- [ ] Collections paginated; expensive/public endpoints rate-limited (+ CAPTCHA where abuse-prone).
- [ ] Idempotency for unsafe-to-retry operations; transactions for multi-write invariants.
- [ ] **Required cases tested:** success · invalid input · error · **authorization denial** (cross-user / non-admin / cross-tenant / untrusted-ID) · regression (`../skills/backend/backend-integration-testing`).

## Fail / Stop

- Missing server-side validation or authorization; unbounded collection; internals leaked in errors; no authz-denial tests.

## Related

Skills: `../skills/backend/README.md`. Reference: `../references/backend/api/`, `auth/`, `authorization/`.
