---
name: third-party-integrations
description: Use to plan integrations with external APIs — client isolation behind an interface, credential handling, timeouts/retries/circuit breaking, error mapping, sandbox vs production, and testing without live calls.
---

# Third-Party Integrations

## Purpose

Integrate external services (payments, messaging, AI, CRM, etc.) so their failures, quirks, and API changes stay contained: one isolated client per provider, resilient calls, mapped errors, and testability without hitting live services.

## When to Use

- When the backend calls any external API.
- **Not** for inbound events (`webhooks`) or file/object storage specifics (`file-storage`) — though their provider clients follow these rules.

## Inputs

- Provider list with the operations used, criticality, and cost per call.
- Failure tolerance per operation (blocking a user vs degradable).

## Discovery Questions

- Per provider: what operations, what rate limits/quotas, what does an outage do to your product?
- Sandbox/test environment available? How do dev/staging avoid production side effects (real emails, real charges)?
- Which calls belong in the request path vs `background-jobs`?

## Responsibilities

- Isolate each provider behind **your own interface** (a service/port the domain calls); provider SDK types never leak into domain code — swapping or mocking stays possible.
- Handle credentials per `backend-security`: env/secret-store, per-environment keys, rotation story, never logged.
- Make every call bounded: explicit **timeouts**, **retries with backoff + jitter only on idempotent/retryable operations**, and circuit-breaking/degradation for critical-path providers — an external 30s hang must not become your 30s hang.
- Map provider errors into your taxonomy (`backend-error-handling`); respect provider rate limits (client-side throttling where needed).
- Separate environments: sandbox keys + fake/sink modes in dev/staging so tests never charge cards or email customers.
- Plan testing: contract-level mocks/stubs of *your interface*, plus a thin recorded/sandbox integration test per provider (`backend-integration-testing`).
- Track usage/cost where metered (AI, SMS) — alert on anomalies (`backend-observability`, ties to `captcha-abuse-prevention` for abuse-driven cost).

## Required Workflow

1. Inventory providers, operations, criticality, quotas.
2. Define the interface per provider; place calls (request path vs background).
3. Set timeout/retry/breaker policy per operation class.
4. Define error mapping + degradation behavior.
5. Set environment separation + test strategy.
6. Specify tests: timeout honored, retry only on retryable, outage degrades as designed, sandbox isolation verified.

## Decision Rules

- Non-idempotent external calls (charges, sends) retry only with provider idempotency keys — otherwise never blind-retry.
- Slow-but-optional providers leave the request path (`background-jobs`).
- Critical-path providers need a recorded outage behavior (fail closed, degrade, queue) — "we go down too" is a decision to make consciously, not discover.
- New providers are stack/vendor decisions — user approval (`../../stack-recommendation`), including data-sharing implications (`../../system/SECURITY_RULES.md`).

## Rules

- One client module per provider; no SDK calls scattered through services.
- Every external call has a timeout — no unbounded awaits.
- Provider responses are inputs: validate shape (`backend-validation`) before trusting.

## Anti-Patterns

- Provider SDK objects passed around the domain.
- Retrying a charge without an idempotency key.
- No timeout — thread-pool/event-loop starvation during provider brownouts.
- Production keys in dev; test runs sending real emails.
- Catching provider errors and rethrowing their raw bodies to clients.

## Validation Checklist

- [ ] Provider inventory with criticality + quotas.
- [ ] Interface isolation per provider.
- [ ] Timeouts, selective retries (+ idempotency keys), breaker/degradation per class.
- [ ] Error mapping into the taxonomy.
- [ ] Environment separation (sandbox/sink) enforced.
- [ ] Tests: timeout, retry policy, outage behavior, no live side effects in CI.

## Definition of Done

A recorded integration design — isolated interfaces, bounded resilient calls, mapped errors, environment separation, and a test strategy that never hits production providers from CI.

## Related Skills

`webhooks`, `background-jobs`, `backend-error-handling`, `backend-security`, `backend-observability`, `backend-integration-testing`, `email-notifications`, `../../dependency-audit` (SDK health).

## Related Knowledge

`../../../knowledge/` (vendor contracts, quotas, data-sharing constraints).

## Related References

`../../../references/backend/integrations/` (provider notes, when populated).

## Context Loading Guidance

- **Requires:** provider/operation inventory, criticality, environment model.
- **Does not require:** full SDK docs, unrelated domains.
- **May load:** `backend-error-handling` (mapping), `background-jobs` (placement).
- **Stop when:** per-provider interface + resilience + test design is recorded.

## Token Efficiency Guidance

One table row per provider (operations, criticality, timeout/retry, outage behavior, sandbox); link vendor docs instead of summarizing them.
