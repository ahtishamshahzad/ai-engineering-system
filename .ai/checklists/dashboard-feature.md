# Checklist: Dashboard Feature

> Verifiable items for an admin/dashboard feature (elevated privilege, sensitive data).

## Pass Criteria

- [ ] **Role/permission gating** on the feature and its actions — server-verified, not UI-hiding only (`../skills/backend/role-permission-design`).
- [ ] **Ownership/tenant scope** enforced on every data read/write (anti-IDOR/BOLA) (`../skills/backend/ownership-authorization`).
- [ ] Bulk/expensive operations are paginated, bounded, and rate-limited where public-facing.
- [ ] Sensitive data displayed per least-privilege; no PII leak in logs/exports.
- [ ] Loading/error/empty/denied states handled; destructive actions confirmed.
- [ ] Audit logging for privileged actions (who did what to what).
- [ ] **Negative tests:** non-admin rejected; cross-tenant/cross-user access rejected; client-supplied IDs untrusted.

## Fail / Stop

- Access enforced only by hiding UI; missing ownership/tenant checks; no negative tests.

## Related

Skills: `../skills/backend/backend-authorization`, `../skills/security/authorization-security`. Reference: `../references/web/dashboard/`.
