---
name: express-foundation
description: Use to plan an Express API foundation after Express is approved — layered structure (routes/controllers/services/data), middleware order, config, error handler, baseline security middleware. Plans only; scaffolds nothing without task approval.
---

# Express Foundation

## Purpose

Define the foundation of an approved Express API: folder layering, middleware pipeline, configuration, central error handling, and baseline libraries — so the "unopinionated" framework still yields a disciplined codebase.

## When to Use

- After `backend-stack-selection` chose Express and Gate 2 approved it.
- **Not** for NestJS (`nestjs-foundation`) or before the stack decision.

## Inputs

- Approved stack decision and architecture (`../../architecture-design`).
- Domain list and expected API surface.

## Discovery Questions

- TypeScript (usually yes) — and which runtime/tooling does the team use?
- Which cross-cutting middleware is needed (auth, rate limiting, logging, CORS)?
- What config/environments exist (`../../environment-audit` conventions)?

## Responsibilities

- Define layering: **routes → controllers/handlers → services (business logic) → data layer** — logic never lives in route handlers.
- Define middleware order: request ID/logging → security headers → CORS → body parsing (with limits) → auth → validation → route → central error handler last.
- Plan config loading with validated env vars (fail fast on missing).
- Plan the central error handler (`backend-error-handling`) and 404 handling.
- Select baseline libraries by need (validation, logging, security headers) — each justified, none auto-installed.

## Required Workflow

1. Confirm approval + architecture inputs.
2. Define the folder/layer structure per domain.
3. Define the middleware pipeline and its order.
4. Define config + env validation approach.
5. Record the foundation plan; implementation follows approved tasks only.

## Decision Rules

- Structure by **domain** (e.g. `users/`, `orders/`) with layers inside, once more than a couple of domains exist; a flat `routes/ services/` split is fine for very small APIs.
- Async route errors must reach the central handler (wrapper or Express 5 behavior) — never `try/catch` per route with ad-hoc responses.
- Keep Express thin: it routes and composes middleware; behavior lives in services.

## Rules

- No scaffolding or dependency installation before task approval (Gate 4).
- Every baseline library justified against a requirement.
- Middleware order is part of the plan — it is behavior, not decoration.

## Anti-Patterns

- Business logic in route handlers or middleware.
- Error responses assembled per-route instead of centrally.
- Unbounded body parsers; missing 404/error handlers.
- Copying a boilerplate repo wholesale without evaluating its choices.

## Validation Checklist

- [ ] Layering defined; logic placed in services.
- [ ] Middleware pipeline ordered and justified.
- [ ] Config/env validation planned (fail fast).
- [ ] Central error handler + 404 planned.
- [ ] Baseline libraries each justified; nothing installed yet.

## Definition of Done

A recorded Express foundation plan — layering, middleware order, config, error handling, justified baseline libraries — ready to drive approved implementation tasks.

## Related Skills

`backend-stack-selection`, `backend-api-architecture`, `backend-error-handling`, `backend-validation`, `backend-security`, `backend-observability`, `../../architecture-design`.

## Related Knowledge

`../../../knowledge/` (team conventions, runtime constraints).

## Related References

`../../../references/backend/express/` (structure examples, when populated).

## Context Loading Guidance

- **Requires:** approved stack decision, domain list, architecture summary.
- **Does not require:** database internals, other packs, unrelated references.
- **May load:** `backend-api-architecture`, `backend-error-handling`.
- **Stop when:** the foundation plan is recorded.

## Token Efficiency Guidance

Plan from the domain list; describe structure as a short tree + rules, not generated files. Delegate details to the specialist skills.
