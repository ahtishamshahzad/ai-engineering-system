---
name: abuse-prevention
description: Use to review whether public and expensive endpoints are protected against automated abuse — rate limiting, CAPTCHA/bot defenses, and cost controls on signup, login, reset, OTP, contact, search, and expensive operations. Verifies abuse defenses hold; distinct from authorization.
---

# Abuse Prevention (Security Review Lens)

## Purpose

Verify the system resists **automated abuse and resource-exhaustion attacks** on its exposed and costly endpoints — the security-review lens on rate limiting and bot defenses. The **build-side design** is `../../backend/rate-limiting` + `../../backend/captcha-abuse-prevention`; this confirms it actually protects the abuse-attractive surface. **Abuse prevention is not authorization** — it limits volume/automation, it doesn't decide rights.

## When to Use

- Reviewing protection of public/expensive endpoints, in `../../security-review` or `threat-modeling` DoS/abuse threats.
- **Not** for building the controls (backend pack) or access decisions (`authorization-security`).

## Inputs

- The rate-limit + CAPTCHA design/implementation (`../../backend/rate-limiting`, `../../backend/captcha-abuse-prevention`).
- Endpoint inventory with cost/sensitivity; threat model's abuse threats.

## Discovery Questions

- Which public/expensive endpoints exist (signup, login, reset, OTP, contact, search, exports/AI/media), and which are protected?
- Are limits **keyed correctly** (IP pre-auth, account post-auth, both for login) and enforced across instances (shared store)?
- Is CAPTCHA/bot verification **server-side** and single-use on abuse-attractive flows?
- Are money/toll endpoints (OTP/SMS senders, metered AI) protected against cost-driven abuse?

## Responsibilities

- Confirm the **abuse-attractive surface is covered**: signup, login (credential stuffing/spraying), password reset, OTP request (toll fraud), contact forms, public search (scraping), and expensive endpoints (exports, AI, media) each have appropriate limits and/or bot defenses.
- Verify **keying + enforcement**: login limited per-account **and** per-IP; pre-auth on IP, post-auth on account; multi-instance uses a shared store (not per-node in-memory that multiplies by instance count).
- Verify **CAPTCHA/bot defenses are server-verified**, single-use, and on the right flows (not everywhere as a UX tax, not missing on OTP/reset).
- Confirm **cost controls** on metered/toll endpoints — abuse there is a financial attack, not just load.
- Confirm the **distinction from authorization**: rate limiting/CAPTCHA reduce volume/automation; they must never be presented as the access control, and access control must never be the only defense on a bot-targeted flow.
- Route gaps to fixes + regression tests (`security-regression-testing`).

## Required Workflow

1. Inventory public/expensive endpoints; map to their protections.
2. Verify coverage of the abuse-attractive flows.
3. Check keying + shared-store enforcement (multi-instance).
4. Verify server-side, single-use CAPTCHA on the right flows + cost controls.
5. Confirm abuse-prevention is layered with (not substituted for) authorization.
6. Record findings; route to fixes + regression tests.

## Decision Rules

- An unprotected signup/reset/OTP/expensive endpoint is a finding — these are the standard abuse targets.
- Per-node in-memory limits behind a load balancer don't work — that's a finding.
- OTP/SMS/metered-AII endpoints without cost controls are financial-risk findings.
- Rate limiting/CAPTCHA presented *as* authorization (or authorization as the only bot defense) is a design finding.

## Rules

- Findings separated Confirmed vs Potential; never declare "secure" (`../../security-review`).
- Abuse prevention verified as distinct from and layered with authorization.
- Each gap maps to a fix + regression test.

## Anti-Patterns

- Public reset/OTP endpoints with no rate limit or CAPTCHA (mail/toll bombing).
- Login limited per-IP only (rotating IPs) or per-account only (lockout DoS on victims).
- In-memory counters behind multiple instances.
- CAPTCHA on every request (UX tax) or missing on the flows that need it.
- Treating a rate limit as "the endpoint is protected" in the authorization sense.

## Validation Checklist

- [ ] Abuse-attractive flows (signup/login/reset/OTP/contact/search/expensive) covered.
- [ ] Correct keying (IP/account/both) + shared-store enforcement.
- [ ] Server-side single-use CAPTCHA on the right flows.
- [ ] Cost controls on metered/toll endpoints.
- [ ] Layered with, not substituted for, authorization.
- [ ] Gaps → fixes → regression tests.

## Definition of Done

A recorded abuse-prevention assessment confirming the public and expensive surface is protected with correctly-keyed, cross-instance limits and server-verified bot defenses (with cost controls) — layered with but distinct from authorization — Confirmed/Potential findings routed to fixes and regression tests, no "secure" claim.

## Related Skills

`../../backend/rate-limiting`, `../../backend/captcha-abuse-prevention`, `authorization-security`, `api-security`, `authentication-security`, `security-regression-testing`, `../../security-review`, `threat-modeling`, `../../devops/monitoring-logging`.

## Related Knowledge

`../../../knowledge/` (abuse history, traffic patterns, cost exposure).

## Related References

`../../../references/security/` (abuse-review checklists, when populated).

## Context Loading Guidance

- **Requires:** rate-limit/CAPTCHA design, endpoint inventory with cost, abuse threats.
- **Does not require:** authorization model depth (`authorization-security`), app internals.
- **May load:** `../../backend/rate-limiting`, `../../backend/captcha-abuse-prevention`.
- **Stop when:** coverage gaps + routed fixes/tests are recorded.

## Token Efficiency Guidance

The endpoint × (limit, key, CAPTCHA, cost-control) coverage matrix is the artifact; focus on the standard abuse targets.
