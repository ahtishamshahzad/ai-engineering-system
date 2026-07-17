---
name: email-notifications
description: Use to plan transactional email and notification delivery — provider choice, template management, sending via background jobs, deliverability (SPF/DKIM/DMARC), bounce/complaint handling, and abuse-safe triggering.
---

# Email & Notifications

## Purpose

Design how the system sends transactional email (and adjacent notifications): provider, templates, async sending, deliverability, and the feedback loop (bounces/complaints) — without letting public flows turn the sender into a spam cannon.

## When to Use

- For verification, password reset, receipts, alerts, digests, and similar transactional sends.
- **Not** for marketing-campaign tooling (different product class) or push-notification platforms (client packs / their own integration).

## Inputs

- Notification inventory: trigger, audience, urgency, content per message type.
- Provider constraints/preferences; sending domain ownership.

## Discovery Questions

- Which messages exist, and which are critical-path (verification, reset) vs best-effort (digest)?
- Who owns the sending domain, and can DNS records (SPF/DKIM/DMARC) be set?
- Which sends are triggered by public endpoints (signup, reset, contact) — abuse exposure?
- Do users need preferences/unsubscribe for non-essential messages?

## Responsibilities

- Choose the provider as a vendor decision (`third-party-integrations` rules: isolation, keys, sandbox/sink in non-prod so staging never emails real users).
- Manage templates: versioned in the repo (or provider-side with a recorded sync story), variables typed/validated, plain-text alternative, consistent sender identities per message class.
- Send **asynchronously** via `background-jobs`: retries with backoff; critical sends (reset, verification) get must-happen treatment + idempotency (no double-send on retry storms; dedupe keys per logical send).
- Plan deliverability: SPF/DKIM/DMARC on the sending domain, separate transactional identity from any marketing traffic, warm-up awareness for new domains.
- Handle **bounces/complaints** via provider webhooks (`webhooks`): suppression list enforced before every send; hard bounces stop future sends.
- Guard abuse: sends triggered by public endpoints (signup, reset, OTP, contact) sit behind `rate-limiting` + `captcha-abuse-prevention` — email-sending endpoints are toll-fraud and spam vectors.
- Respect preferences: essential/security messages always send; everything else honors user settings + unsubscribe.

## Required Workflow

1. Build the message inventory (trigger, criticality, audience, identity).
2. Choose provider + environment sink strategy.
3. Design template management + validation.
4. Wire async sending with per-class retry/idempotency.
5. Set up deliverability records + bounce/complaint suppression.
6. Confirm abuse protection on triggering endpoints.
7. Specify tests: template variables validated, suppression respected, no duplicate critical sends, staging sends hit the sink.

## Decision Rules

- Critical-path emails (verification/reset) are must-happen jobs with monitoring; a digest may drop, a reset email may not.
- Non-prod environments never send to real addresses — sink/allowlist enforced at the client wrapper, not by discipline.
- Reset/verification emails don't reveal account existence in triggering-endpoint responses (`backend-authentication`).
- One logical send = one idempotency key; retries re-attempt delivery, not re-decide sending.

## Rules

- All sends go through the isolated provider client — no inline SMTP calls in handlers.
- Suppression list checked on every send path.
- Templates never interpolate unvalidated user HTML (injection into email).

## Anti-Patterns

- Sending inline in the request (slow + lost on failure).
- Staging accidentally emailing production users.
- Ignoring bounces until the provider suspends the account.
- Public reset endpoint without rate limit/CAPTCHA — attacker-driven mail bombing.
- Critical and bulk mail on one identity, digests dragging reset deliverability down.

## Validation Checklist

- [ ] Message inventory with criticality + identity per class.
- [ ] Provider isolated; non-prod sink enforced.
- [ ] Templates versioned, variables validated, text alternative.
- [ ] Async sends with retry + idempotency per class.
- [ ] SPF/DKIM/DMARC planned; bounce/complaint suppression wired.
- [ ] Triggering public endpoints abuse-protected.

## Definition of Done

A recorded email design — provider isolation with environment sinks, versioned validated templates, idempotent async sending per criticality class, deliverability records, suppression handling, and abuse-protected triggers.

## Related Skills

`third-party-integrations`, `background-jobs`, `webhooks` (bounce events), `rate-limiting`, `captcha-abuse-prevention`, `backend-authentication` (verification/reset flows), `backend-observability`.

## Related Knowledge

`../../../knowledge/` (message inventory, domain ownership).

## Related References

`../../../references/backend/integrations/` (provider/template notes, when populated).

## Context Loading Guidance

- **Requires:** message inventory, provider constraints, triggering-endpoint list.
- **Does not require:** template copywriting, marketing tooling.
- **May load:** `background-jobs` (send jobs), `webhooks` (feedback loop).
- **Stop when:** the send pipeline + deliverability + abuse design is recorded.

## Token Efficiency Guidance

The message-inventory table (trigger, class, retry, identity) drives the design; keep deliverability to the checklist, not an email-infrastructure essay.
