---
name: rate-limiting
description: Use to plan rate limiting per endpoint class — signup, login, password reset, OTP, contact forms, public search, and expensive endpoints — with keys (IP/account/session), algorithms, stores, and 429 behavior. Abuse prevention, not authorization.
---

# Rate Limiting

## Purpose

Protect endpoints from abuse and overload with per-class limits: the right key, algorithm, window, and store per endpoint type. **This is abuse prevention — it throttles *how often*; it never decides *who may* (that's `backend-authorization`).**

## When to Use

- Whenever the API has public endpoints, auth flows, or expensive operations.
- **Not** as a substitute for authorization, quotas-as-billing (a product feature), or CAPTCHA (complementary — `captcha-abuse-prevention`).

## Inputs

- Endpoint inventory with cost/sensitivity (`rest-api-design`).
- Deployment shape (single node vs multiple — determines store).

## Discovery Questions

- Which endpoints are public-unauthenticated, auth-flow, or expensive?
- What legitimate peak usage must limits *not* break (retries, mobile refreshes, NAT-shared IPs)?
- Single instance (in-memory store possible) or multiple (shared store, e.g. Redis, required)?

## Responsibilities

- Classify endpoints and set limits per class — tight to loose:
  - **Login**: per-account *and* per-IP (protects both credential stuffing and spraying); lockout/backoff policy coordinated with `backend-authentication`.
  - **Signup**: per-IP, low; pairs with CAPTCHA.
  - **Password reset / OTP request**: per-account and per-IP, very tight; OTP *verification* attempts capped per code.
  - **Contact forms / public writes**: per-IP, low; pairs with CAPTCHA.
  - **Public search / listing**: per-IP moderate; cost-aware for heavy queries.
  - **Expensive endpoints** (exports, reports, media processing, AI calls): per-account, small; consider queueing instead of rejecting (`background-jobs`).
  - **General authenticated API**: per-account baseline as a safety net.
- Choose keys deliberately: IP (pre-auth; beware shared NATs/proxies — trust the right forwarded header only from your proxy), account/user ID (post-auth), or composite.
- Choose algorithm (fixed window, sliding window, token bucket) and store (in-memory only for single-node; shared store across instances).
- Define the 429 response in the standard error shape with `Retry-After` (`backend-error-handling`); log/alert on sustained limiting (`backend-observability`).

## Required Workflow

1. Classify endpoints into the classes above.
2. Set key + limit + window per class, sized against legitimate peaks.
3. Pick algorithm + store for the deployment shape.
4. Define 429 behavior and monitoring.
5. Specify tests: limit trips at N+1, resets after window, keyed correctly (user B unaffected by user A's exhaustion).

## Decision Rules

- Rate limiting **complements** authorization and CAPTCHA — it reduces attempt volume; it must never be the only control on a protected or bot-targeted action.
- Pre-auth endpoints key on IP (imperfect but available); post-auth on account; login on both.
- Multi-instance deployments need a shared store — per-node in-memory limits multiply by instance count.
- Fail open or closed is a decision: auth flows fail closed (limit errors reject); read endpoints may fail open — record the choice.

## Rules

- Every public and expensive endpoint has a recorded limit or a recorded reason it doesn't.
- Derive client IP only from your own proxy's trusted header configuration.
- Limits, windows, and stores live in config, not scattered constants.

## Anti-Patterns

- One global limit for all endpoints.
- Treating a rate limit as an authorization control ("only 5 tries, so it's protected").
- Per-IP-only login limits (rotating IPs defeat them) or per-account-only (lockout DoS on victims).
- In-memory counters behind a load balancer.
- Trusting `X-Forwarded-For` from the public internet.

## Validation Checklist

- [ ] Endpoints classified; limits per class recorded (signup, login, reset, OTP, contact, search, expensive covered).
- [ ] Keys chosen per class (IP/account/composite).
- [ ] Algorithm + store fit the deployment shape.
- [ ] 429 shape + `Retry-After` + monitoring defined.
- [ ] Tests: trip, reset, key isolation.
- [ ] Not used as an authorization substitute anywhere.

## Definition of Done

A recorded per-class rate-limit design — keys, limits, algorithm, store, 429 behavior, monitoring — covering all public/auth/expensive endpoints, with tests, explicitly complementing (never replacing) authorization and CAPTCHA.

## Related Skills

`captcha-abuse-prevention`, `backend-authentication`, `backend-authorization`, `backend-error-handling`, `backend-observability`, `background-jobs`, `backend-security`.

## Related Knowledge

`../../../knowledge/` (traffic patterns, deployment shape).

## Related References

`../../../references/backend/abuse/` (limit tables, when populated).

## Context Loading Guidance

- **Requires:** endpoint inventory with classes, deployment shape.
- **Does not require:** authorization matrix detail, handler code.
- **May load:** `captcha-abuse-prevention` (paired flows), `backend-error-handling`.
- **Stop when:** the per-class limit table + tests are recorded.

## Token Efficiency Guidance

The endpoint-class × (key, limit, window, store) table is the deliverable; keep algorithm discussion to the chosen one.
