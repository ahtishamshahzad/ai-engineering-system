---
name: backend-error-handling
description: Use to plan backend error handling — an error taxonomy (operational vs programmer errors), one central handler, a single safe response shape, correct status mapping, and logging with correlation IDs. Internals never leak to clients.
---

# Backend Error Handling

## Purpose

Define one way errors are represented, propagated, mapped to responses, and logged — so clients get a stable, safe shape and operators get diagnosable detail.

## When to Use

- When establishing a backend foundation, or when error responses are inconsistent/leaky.
- **Not** for client-side error UX (client packs).

## Inputs

- Endpoint design + status-code conventions (`rest-api-design`).
- Observability plan (`backend-observability`).

## Discovery Questions

- What error classes does the domain need (not-found, conflict, validation, auth, rate-limited, upstream-failed)?
- What may a client see per class — and what must it never see?
- How are errors correlated with logs (request ID)?

## Responsibilities

- Define an **error taxonomy**: typed operational errors (expected: validation, not-found, forbidden, conflict, rate-limited, upstream) vs programmer errors (bugs: crash loudly, alert, never mask as 4xx).
- Route everything to **one central handler** (Express error middleware / Nest exception filter) that maps error type → status code → the **single response shape** (machine-readable code, safe message, optional field details, request ID).
- Ensure async paths reach the handler (async route wrappers, job/queue error hooks — `background-jobs` failures are errors too).
- Log at the handler with severity by class; stack traces and internals go to logs (`backend-observability`), **never** to clients.
- Map upstream/third-party failures (`third-party-integrations`) to your own error types — don't proxy foreign error bodies.

## Required Workflow

1. Define the taxonomy + status mapping table.
2. Define the response shape once (align with `api-contracts`).
3. Wire the central handler; verify async and non-HTTP paths reach it.
4. Set logging severity + correlation ID per class.
5. Add tests: each error class returns its mapped status + shape; no stack traces in responses.

## Decision Rules

- Operational errors are values/types thrown intentionally; anything unrecognized is a programmer error → 500 + alert.
- 4xx tells the client what *it* can fix; 5xx admits fault and says nothing internal.
- Auth-related mapping (401 vs 403 vs 404-for-hiding) follows the authorization design (`backend-authorization`, `ownership-authorization`).
- Retryable conditions (429, 503) include `Retry-After` where honest.

## Rules

- One response shape, everywhere — including validation, auth, and rate-limit errors.
- No `catch` that swallows; catch to translate or enrich, else let it propagate.
- Error messages never contain secrets, SQL, file paths, or stack frames.

## Anti-Patterns

- Per-route error response assembly.
- Programmer errors dressed as 400s, hiding bugs.
- Swallowed promise rejections; unhandled queue-job failures.
- Proxying upstream error bodies to your clients.
- Different shapes for validation vs other errors.

## Validation Checklist

- [ ] Taxonomy + status mapping recorded.
- [ ] Single response shape in the contract.
- [ ] Central handler wired; async + job paths covered.
- [ ] Logging severity + request ID per class.
- [ ] Tests prove mapping and non-leakage.

## Definition of Done

A recorded taxonomy, one central handler, one safe response shape, correct status mapping, and correlated logging — proven by tests, with nothing internal leaking.

## Related Skills

`backend-validation`, `rest-api-design`, `api-contracts`, `backend-observability`, `backend-security`, `background-jobs`, `third-party-integrations`.

## Related Knowledge

`../../../knowledge/` (domain error cases).

## Related References

`../../../references/backend/` (error-shape conventions, when populated).

## Context Loading Guidance

- **Requires:** endpoint conventions, error classes the domain needs.
- **Does not require:** every handler's code, client error UX.
- **May load:** `backend-observability`, `api-contracts`.
- **Stop when:** taxonomy, handler wiring, and tests are planned/recorded.

## Token Efficiency Guidance

Express the design as the taxonomy→status→shape table; that table is most of the skill's output.
