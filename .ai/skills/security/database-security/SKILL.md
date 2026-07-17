---
name: database-security
description: Use to review database security from an audit/threat lens — least-privilege access, network exposure, injection surface, encryption of sensitive data, tenant isolation depth, and PII handling — verifying the data-layer hardening holds. The security-review lens; the database pack owns the build-side design.
---

# Database Security (Security Review Lens)

## Purpose

Verify the data store's security posture actually holds against attack: least-privilege access, no network exposure, an injection-free data layer, encryption of sensitive data, real tenant isolation, and disciplined PII handling. The **build-side design** is `../../database/database-security`; this security-pack skill is the **review/audit lens** on it, feeding `../../security-review`.

## When to Use

- Auditing database security, in `../../security-review` or `threat-modeling` data-disclosure threats.
- **Not** for designing the data-layer hardening (`../../database/database-security`) — this verifies it.

## Inputs

- The database security design/implementation (`../../database/database-security`).
- Data-sensitivity map, tenancy model, deployment topology.

## Discovery Questions

- What privileges does the **app's runtime account** actually hold — CRUD-only, or DDL/superuser it doesn't need?
- Is the database **network-reachable** from anywhere beyond the app (public listener, exposed port)?
- Is the data layer **parameterized-only**, or are there raw/string-built queries (incl. Mongo operator injection)?
- What sensitive data exists, is it **encrypted** appropriately, and does account deletion truly remove PII?

## Responsibilities

- **Least privilege**: confirm the runtime account is CRUD-scoped (no DDL/superuser); migrations use a separate account; human/analytics access is limited and audited (`../../database/database-security`).
- **Network exposure**: confirm private-network-only, TLS-enforced, no public listener — an internet-reachable database is a critical finding.
- **Injection surface**: audit for parameterized-only access; flag string-built SQL and unvalidated object/operator injection in NoSQL (`../../backend/backend-validation` blocks `{$gt:''}`-shaped input); overlaps with `api-security`.
- **Encryption**: sensitive data encrypted at rest, TLS in transit, and application-level field encryption for the truly sensitive (tokens, stored user secrets) with key management (`secrets-audit`).
- **Tenant isolation depth**: confirm scoped queries are enforced (`authorization-security`, `../../backend/ownership-authorization`) and evaluate whether the risk profile warrants DB-enforced isolation (row-level security / per-tenant schemas).
- **PII discipline**: no PII in logs/backups/dev seeds (`privacy-review`, `../../testing/test-data-management`); retention + tested deletion paths exist.
- Route findings to fixes + regression tests (`security-regression-testing`).

## Required Workflow

1. Review account privileges (runtime, migration, human, analytics).
2. Verify network isolation + TLS.
3. Audit the data layer for injection (SQL + NoSQL operator).
4. Verify encryption per data class + key management.
5. Assess tenant-isolation depth + PII handling/deletion.
6. Record findings; route to fixes + regression tests.

## Decision Rules

- Public-reachable database or app-account-with-DDL/superuser = confirmed high-severity findings.
- Any string-built query is an injection finding regardless of "trusted" input.
- Field-level encryption is for data whose leak survives a full dump — verify it's applied where the sensitivity demands, not encrypt-everything-into-mush.
- Real production data in any lower environment is a finding (`privacy-review`).

## Rules

- Never print secrets or real data during the audit — location + severity only.
- Findings separated Confirmed vs Potential; never declare "secure" (`../../security-review`).
- Each finding maps to a fix + regression test; delegate build-side design to `../../database/database-security`.

## Anti-Patterns

- Passing a database on a public IP.
- App connecting as owner/superuser "temporarily forever."
- The one legacy raw query nobody re-checks for injection.
- "Encrypted at rest" (disk) presented as answering field-sensitivity.
- PII in logs, backups, and dev seeds; deletion that flips a boolean.

## Validation Checklist

- [ ] Least-privilege accounts verified (runtime/migration/human/analytics).
- [ ] Network private + TLS; no public listener.
- [ ] Parameterized-only data layer (SQL + NoSQL) verified.
- [ ] Encryption per class + key management confirmed.
- [ ] Tenant-isolation depth assessed; PII handling/deletion verified.
- [ ] Findings → fixes → regression tests.

## Definition of Done

A recorded database security audit — access privileges, network exposure, injection surface, encryption, tenant isolation, PII handling — with Confirmed/Potential findings routed to fixes and regression tests, delegating build-side design to the database pack, and no "secure" claim.

## Related Skills

`../../database/database-security`, `authorization-security`, `../../backend/ownership-authorization`, `api-security`, `secrets-audit`, `privacy-review`, `../../testing/test-data-management`, `security-regression-testing`, `../../security-review`, `threat-modeling`.

## Related Knowledge

`../../../knowledge/` (data classes, tenancy model, topology).

## Related References

`../../../references/security/` (database review checklists, when populated).

## Context Loading Guidance

- **Requires:** the database security design/impl, sensitivity map, topology.
- **Does not require:** re-designing the hardening (delegate), app feature code.
- **May load:** `../../database/database-security`, `authorization-security`.
- **Stop when:** findings + routed fixes/tests are recorded.

## Token Efficiency Guidance

The finding table (area → issue → severity → fix) is the artifact; never echo real data or secrets. Delegate build design rather than restating it.
