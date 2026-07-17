---
name: captcha-abuse-prevention
description: Use to plan CAPTCHA and bot-abuse prevention for public endpoints — signup, login, password reset, OTP, contact forms, public search, expensive endpoints. Server-side verification, layered with rate limiting; never a substitute for authorization.
---

# CAPTCHA & Abuse Prevention

## Purpose

Keep bots and automated abuse off public endpoints using CAPTCHA and complementary bot signals — verified **server-side**, layered with `rate-limiting`. **Abuse prevention filters non-human/mass traffic; it is not authorization and grants nothing.**

## When to Use

- For public, unauthenticated, or bot-attractive flows: signup, login, password reset, OTP request, contact forms, public search, expensive endpoints.
- **Not** for deciding access rights (`backend-authorization`) and not on flows where friction is unjustified by observed or expected abuse.

## Inputs

- Public-endpoint inventory + rate-limit design (`rate-limiting`).
- UX constraints (client platforms, accessibility requirements).

## Discovery Questions

- Which flows are bot targets today or at launch (signup spam, credential stuffing, OTP pumping, contact spam, scraping)?
- What CAPTCHA style fits the UX (invisible/score-based vs challenge) and which flows justify hard friction?
- What non-CAPTCHA signals are available (honeypot fields, submit timing, disposable-email checks, IP reputation)?

## Responsibilities

- Map flows to protections, escalating with abuse pressure:
  - **Signup**: CAPTCHA (bots create accounts for everything downstream) + disposable-email policy + rate limit.
  - **Login**: score-based/invisible CAPTCHA, escalating to challenge after failures — layered on the login rate limits.
  - **Password reset / OTP request**: CAPTCHA before send (each send costs money and spams victims) + tight rate limits; OTP pumping is a financial attack.
  - **Contact forms**: CAPTCHA or honeypot+timing at minimum.
  - **Public search / scraping targets**: prefer rate limits + bot heuristics; CAPTCHA only under attack (search friction is costly).
  - **Expensive endpoints**: prefer authentication + rate limits/queues; CAPTCHA if they must stay public.
- **Verify tokens server-side** with the provider on every protected request — client-side "verified" flags are theater; tokens are single-use and short-lived.
- Plan failure handling: provider outage policy (fail open vs closed per flow — record it), score thresholds, escalation ladder (invisible → challenge → block).
- Keep accessibility in view: audio/alternative challenges; never CAPTCHA-only lockout for assistive-tech users.
- Monitor effectiveness: solve rates, failure spikes, abuse-through rates (`backend-observability`).

## Required Workflow

1. Inventory bot-attractive flows; assess abuse pressure per flow.
2. Choose protection per flow (CAPTCHA style, complementary signals) and escalation ladder.
3. Design server-side verification (token flow, single-use, timeout, outage policy).
4. Layer with rate limits — neither replaces the other.
5. Specify tests: missing/invalid/replayed token rejected server-side; honeypot triggers rejection.

## Decision Rules

- Server verification is the control; the widget is UX.
- CAPTCHA reduces *automation*, rate limits reduce *volume*, authorization decides *rights* — three layers, no substitutions in either direction.
- Start invisible/score-based; escalate friction only where signals or incidents justify it.
- Auth-flow verification fails **closed** (no bypass when the provider is down without an explicit, recorded exception); low-risk forms may fail open.

## Rules

- Every protected flow verifies the token server-side, single-use.
- Secrets (provider keys) follow `backend-security` handling.
- Record per-flow decisions including "no CAPTCHA because…" — absence is a decision too.

## Anti-Patterns

- Client-side-only CAPTCHA validation.
- Treating a solved CAPTCHA as authentication or authorization.
- CAPTCHA on every request instead of abuse-attractive flows (UX tax, no gain).
- No protection on OTP/SMS senders (toll fraud magnet).
- Accepting the same CAPTCHA token repeatedly (replay).

## Validation Checklist

- [ ] Bot-attractive flows inventoried (signup, login, reset, OTP, contact, search, expensive considered).
- [ ] Protection + escalation per flow recorded.
- [ ] Server-side, single-use verification designed with outage policy.
- [ ] Layered with rate limits; distinct from authorization everywhere.
- [ ] Accessibility fallback present.
- [ ] Tests: invalid/missing/replayed tokens rejected.

## Definition of Done

A recorded per-flow abuse-prevention design — CAPTCHA style, complementary signals, server-side single-use verification, outage policy, escalation ladder — layered with rate limiting and never standing in for authorization.

## Related Skills

`rate-limiting`, `backend-authentication`, `backend-authorization`, `backend-security`, `backend-observability`, `third-party-integrations` (provider client), `email-notifications` (send-triggering flows).

## Related Knowledge

`../../../knowledge/` (abuse history, UX constraints).

## Related References

`../../../references/backend/abuse/` (flow-protection table, when populated).

## Context Loading Guidance

- **Requires:** public-flow inventory, rate-limit design, UX constraints.
- **Does not require:** authorization matrix, provider SDK docs.
- **May load:** `rate-limiting` (layering), `backend-observability` (monitoring).
- **Stop when:** per-flow protections + verification design + tests are recorded.

## Token Efficiency Guidance

The flow × protection table carries the design; keep provider specifics to a link in references.
