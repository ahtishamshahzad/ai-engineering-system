---
name: database-security
description: Use to plan database security — least-privilege credentials, network isolation, injection prevention (parameterized-only), encryption at rest/in transit, PII classification and minimization, tenant isolation at the data layer, and access auditing.
---

# Database Security

## Purpose

Protect the data store itself: who can connect, what the app account can do, how injection is made impossible, how sensitive data is classified and encrypted, and how access is audited. Complements `../backend/backend-security` (app layer) and feeds `../../security-review`.

## When to Use

- When provisioning a database, hardening an existing one, or handling new sensitive data classes.
- **Not** as the release audit itself (`../../security-review`).

## Inputs

- Data-sensitivity map (`../backend/backend-security`), tenancy model (`../backend/ownership-authorization`).
- Deployment topology (who/what can reach the database), compliance constraints.

## Discovery Questions

- What data classes live here (credentials, PII, payment/financial, health) and what do compliance rules demand of each?
- Who connects: app, migrations, humans, analytics — with what privileges each?
- Is the database network-reachable from anywhere beyond the app (it shouldn't be)?

## Responsibilities

- **Least-privilege accounts**: the app's runtime account gets CRUD on its schema only — no DDL, no superuser (migrations run under a separate, deploy-scoped account — `database-migrations`); humans get personal, audited, read-limited access; analytics gets replicas/views, not the primary's write account.
- **Network isolation**: private network/VPC only, no public listener; TLS on connections; access from production paths only — dev machines don't hold prod credentials.
- **Credentials**: secret store/env per `../backend/backend-security`, rotated, never in code/logs/dumps; per-environment separation absolute.
- **Injection prevention**: parameterized queries *only* — ORMs default to this, raw escape hatches (`$queryRaw`, `sql` templates, Mongo operator injection via unvalidated objects) hold the discipline (`../backend/backend-validation` blocks operator-shaped input like `{$gt: ''}`).
- **Encryption**: at rest (storage/provider level), in transit (TLS), and **application-level encryption for the truly sensitive fields** (tokens, secrets users store with you) — with key management stated; hashing where reversibility isn't needed (`../backend/backend-authentication` credentials).
- **PII discipline**: classify columns/fields; minimize collection; define retention + deletion paths (account-deletion actually deletes/anonymizes — including backups policy awareness `backup-recovery`); PII never in logs (`../backend/backend-observability`) or dev seeds (`seed-data`).
- **Tenant isolation depth**: scoped queries are the floor (`ownership-authorization`); evaluate row-level security or per-tenant schemas where the risk profile demands database-enforced isolation — recorded decision either way.
- **Auditing**: who connected, admin/DDL actions, bulk exports — logged and reviewable.

## Required Workflow

1. Classify data; map compliance demands per class.
2. Define accounts + privileges per connector; isolate the network.
3. Verify parameterized-only access across the data layer (audit raw usages).
4. Set encryption per class (rest/transit/field) with key management.
5. Define PII retention/deletion paths.
6. Record the tenant-isolation depth decision; wire access auditing.
7. Hand to `../../security-review` before release.

## Decision Rules

- The app account can't do what the app never does — DDL rights in runtime accounts are standing self-harm.
- Any string-built query is a defect regardless of "we control that input."
- Field-level encryption is for data whose leak survives a database dump; don't encrypt-everything into unqueryable mush — classify first.
- Real production data never seeds lower environments (`seed-data`).

## Rules

- Never print/commit credentials while working; flag + rotate on discovery.
- Every raw-query usage is inventoried and reviewed.
- Deletion paths are tested, not asserted.

## Anti-Patterns

- App connecting as superuser/owner "temporarily forever."
- Database on a public IP with password auth.
- String interpolation in the one legacy raw query nobody reviews.
- PII in logs, fixtures, and three abandoned analytics exports.
- "Encrypted at rest" (disk) presented as the answer to field-sensitivity questions.
- Account deletion that flips a boolean and keeps everything.

## Validation Checklist

- [ ] Data classified; compliance mapped.
- [ ] Least-privilege accounts per connector; migration account separate.
- [ ] Network private; TLS enforced.
- [ ] Parameterized-only verified; raw usages inventoried.
- [ ] Encryption per class + key management recorded.
- [ ] PII retention/deletion paths defined and tested.
- [ ] Tenant-isolation depth decision recorded; auditing wired.

## Definition of Done

A recorded database-security posture — least-privilege access, isolated network, injection-proof data layer, class-appropriate encryption, tested PII deletion, explicit tenant-isolation depth, and auditing — queued for security review.

## Related Skills

`../backend/backend-security`, `../../security-review`, `../backend/ownership-authorization`, `../backend/backend-validation`, `../backend/backend-authentication`, `backup-recovery`, `seed-data`, `database-migrations`, `../../environment-audit`.

## Related Knowledge

`../../../knowledge/` (data classes, compliance regime, topology).

## Related References

`../../../references/database/security/` (classification table, when populated).

## Context Loading Guidance

- **Requires:** sensitivity map, tenancy model, topology, connector list.
- **Does not require:** application feature code, query-by-query review (sample raw usages).
- **May load:** `backup-recovery` (backup encryption/PII), `../../security-review` (hand-off).
- **Stop when:** the posture is recorded and handed to review.

## Token Efficiency Guidance

The classification table (field class → encryption → retention → access) plus the account-privilege matrix carry the design; never echo actual secrets or real data while working.
