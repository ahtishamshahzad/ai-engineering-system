---
name: backend-security
description: Use to plan backend security hardening — secrets handling, security headers, CORS, injection defenses, dependency risk, request limits, and audit logging. Coordinates the specialist skills (validation, authz, rate limiting) and feeds security-review.
---

# Backend Security

## Purpose

Establish the backend's baseline security posture and coordinate the specialist skills that own each control. Assessment of a finished system belongs to `../../security-review`; this skill designs the controls in.

## When to Use

- When establishing a backend foundation or hardening an existing one.
- **Not** as the audit itself (`../../security-review`) and not for per-domain detail owned by the specialists below.

## Inputs

- Endpoint inventory + data sensitivity map; deployment shape.
- Findings from `existing-backend-audit` / `../../environment-audit` if present.

## Discovery Questions

- What sensitive data classes exist (credentials, PII, payment data), and where do they flow?
- Who are the callers (browsers → CORS/cookies matter; servers → key management matters)?
- What regulatory/compliance constraints apply?

## Responsibilities

- **Secrets**: env/secret-store only, validated at boot, never in code/logs/errors/git history; per-environment values; rotation story (`../../environment-audit`).
- **Transport & headers**: TLS assumed at the edge; security headers (HSTS, nosniff, frame denial, minimal CSP for any served HTML); correct trust of proxy headers.
- **CORS**: explicit allowed origins per environment — never wildcard with credentials.
- **Injection defenses**: parameterized queries only (`../../database/database-security`), no shell/`eval` on input, path traversal blocked on any filesystem access; output encoding where HTML is produced.
- **Request hygiene**: body-size limits, upload constraints (`file-storage`), pagination caps — resource exhaustion is a security issue.
- **Cookies/CSRF**: httpOnly/secure/sameSite; CSRF protection for cookie-authenticated state changes.
- **Audit logging**: security-relevant events (logins, permission changes, admin actions, data exports) logged with actor + target, PII-safe (`backend-observability`).
- **Delegate and verify**: input validation → `backend-validation`; authn → `backend-authentication`; authz + IDOR → `backend-authorization`/`ownership-authorization`; abuse → `rate-limiting`/`captcha-abuse-prevention`; dependencies → `../../dependency-audit`.

## Required Workflow

1. Map sensitive data classes and their flow.
2. Set the baseline controls above for the chosen framework.
3. Confirm each delegated area has its skill's design recorded.
4. Define audit-logging events.
5. Hand the whole surface to `../../security-review` before release (Gate).

## Decision Rules

- Defense in depth: no control is "covered elsewhere" — validation AND authz AND limits.
- Default deny: new endpoints inherit strict defaults (auth required, tight CORS, limits) and relax explicitly.
- Anything readable by the client is public: no secrets in responses, headers, or verbose errors (`backend-error-handling`).
- Compliance constraints are requirements — surface them at planning, not release.

## Rules

- Never print or commit a secret while working — flag + rotate if found.
- Security controls are configuration-as-code, reviewable — not console-clicked.
- No security claim without `../../security-review` — this skill designs, it doesn't certify.

## Anti-Patterns

- `cors({ origin: true, credentials: true })`.
- Secrets in code "temporarily."
- String-built SQL/shell commands from input.
- Verbose stack traces in production responses.
- Treating rate limiting or CAPTCHA as the authorization story.
- One giant "security later" ticket.

## Validation Checklist

- [ ] Sensitive-data map recorded.
- [ ] Secrets: storage, boot validation, rotation story.
- [ ] Headers + CORS explicit per environment.
- [ ] Injection defenses + request limits set.
- [ ] Cookie/CSRF posture set for browser callers.
- [ ] Audit events defined, PII-safe.
- [ ] Delegated areas each have recorded designs.

## Definition of Done

A recorded baseline-hardening design — secrets, headers, CORS, injection defenses, request hygiene, audit logging — with every delegated control owned by its specialist skill and the surface queued for security review.

## Related Skills

`../../security-review`, `backend-validation`, `backend-authentication`, `backend-authorization`, `ownership-authorization`, `rate-limiting`, `captcha-abuse-prevention`, `../../database/database-security`, `../../dependency-audit`, `../../environment-audit`.

## Related Knowledge

`../../../knowledge/` (data classes, compliance constraints).

## Related References

`../../../references/backend/security/` (hardening notes, when populated).

## Context Loading Guidance

- **Requires:** data-sensitivity map, caller types, deployment shape.
- **Does not require:** each specialist's full detail (verify their outputs exist).
- **May load:** `../../security-review` (hand-off), `../../environment-audit`.
- **Stop when:** the baseline design is recorded and delegations confirmed.

## Token Efficiency Guidance

Work the checklist; link specialist skill outputs instead of restating them. The sensitive-data flow map is the one artifact worth drawing.
