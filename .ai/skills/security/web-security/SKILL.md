---
name: web-security
description: Use to review web/browser security — XSS (output encoding, CSP), CSRF for cookie auth, secure cookie flags, security headers, clickjacking, open redirects, and no secrets in client bundles. The browser-facing security lens; API-layer concerns are api-security.
---

# Web Security

## Purpose

Assess the browser-facing attack surface: cross-site scripting, cross-site request forgery, cookie/session safety, and the headers/policies that harden a web app — the client-side counterpart to `api-security`.

## When to Use

- Reviewing a web/dashboard frontend (and its browser-facing backend behavior) for security, in `../../security-review`.
- **Not** for API-layer risks (`api-security`) or mobile (`mobile-security`) — though headers/CORS overlap with `api-security`.

## Inputs

- The web app + how it renders user/content data and authenticates (cookies vs tokens).
- Backend response headers, CORS, and cookie configuration.

## Discovery Questions

- Where is user/external content rendered — is output encoded, and is dangerous HTML injection (`dangerouslySetInnerHTML`, raw templating) present?
- Is there a **Content Security Policy**, and does it meaningfully constrain scripts?
- If auth uses cookies, is **CSRF** protected and are cookies `httpOnly`/`secure`/`sameSite`?
- Are security headers present (HSTS, X-Content-Type-Options, frame-ancestors/X-Frame-Options)? Any open redirects?

## Responsibilities

- **XSS**: verify output encoding by default (frameworks help, but `dangerouslySetInnerHTML`/`v-html`/raw injection are findings); user-controlled HTML sanitized; a **Content Security Policy** as defense-in-depth constraining script sources.
- **CSRF**: for cookie-based auth, state-changing requests are CSRF-protected (tokens / `sameSite` cookies); token-in-header auth is inherently less CSRF-prone — confirm which model and that it's coherent.
- **Cookies/session**: `httpOnly` (no JS access), `secure` (HTTPS-only), `sameSite` set; session invalidation on logout (`authentication-security`).
- **Headers**: HSTS, `X-Content-Type-Options: nosniff`, frame protection (clickjacking), a reviewed CSP; correct CORS (no wildcard-with-credentials — shared with `api-security`).
- **Redirects/navigation**: no open redirects (user-controlled redirect targets validated against an allowlist); `target=_blank` gets `rel=noopener`.
- **No secrets in the bundle**: anything shipped to the browser is public — flag embedded secrets (`../../devops/environment-management` boundary).
- **User-uploaded content** served safely (separate origin, correct content-type/disposition — `../../backend/file-storage`).
- Route findings to fixes + regression tests (`security-regression-testing`).

## Required Workflow

1. Review rendering paths for XSS; check for a meaningful CSP.
2. Determine the auth model; verify CSRF handling + cookie flags.
3. Check security headers + clickjacking protection.
4. Check redirects/navigation + bundle secret exposure.
5. Verify safe serving of user content; record findings + tests.

## Decision Rules

- Framework auto-escaping helps, but every `dangerouslySetInnerHTML`/raw-HTML path is a finding until proven sanitized.
- Cookie auth without CSRF protection is a confirmed finding; token-in-header changes the model — verify coherence, don't assume.
- A CSP is defense-in-depth, not a substitute for encoding — check both.
- Anything in the client bundle is public — embedded secrets are always findings.

## Rules

- Findings separated Confirmed vs Potential; never declare the app "secure" (`../../security-review`).
- Each finding maps to a fix + a regression test.
- Bundle secrets, XSS sinks, and missing CSRF protection are treated as real findings, not style notes.

## Anti-Patterns

- Rendering user content via `dangerouslySetInnerHTML` without sanitization.
- Cookie auth with no CSRF defense.
- Non-`httpOnly`/`secure`/`sameSite` session cookies.
- Missing security headers / no CSP.
- Open redirects from user-controlled URLs.
- Secrets shipped in the client bundle.

## Validation Checklist

- [ ] Output encoding verified; XSS sinks sanitized; meaningful CSP.
- [ ] CSRF handled for the auth model; cookies httpOnly/secure/sameSite.
- [ ] Security headers + clickjacking protection present.
- [ ] Redirects validated; no bundle secrets.
- [ ] User content served safely; findings → regression tests.

## Definition of Done

A recorded web security assessment — XSS/CSP, CSRF, cookie/session safety, headers, redirects, and bundle secret exposure — with Confirmed/Potential findings routed to fixes and regression tests, and no "secure" claim.

## Related Skills

`api-security`, `authentication-security`, `../../backend/backend-security`, `../../backend/file-storage`, `../../devops/environment-management`, `secrets-audit`, `security-regression-testing`, `../../security-review`, `threat-modeling`.

## Related Knowledge

`../../../knowledge/` (rendering paths, auth model, browser threats).

## Related References

`../../../references/security/` (web review checklists, when populated).

## Context Loading Guidance

- **Requires:** the web app's rendering + auth, backend headers/cookies/CORS.
- **Does not require:** API business logic depth (`api-security`), mobile concerns.
- **May load:** `api-security`, `authentication-security`.
- **Stop when:** findings + routed fixes/tests are recorded.

## Token Efficiency Guidance

The finding table (category → location → severity → fix) is the artifact; focus on XSS sinks, CSRF, and cookie flags where browser breaches concentrate.
