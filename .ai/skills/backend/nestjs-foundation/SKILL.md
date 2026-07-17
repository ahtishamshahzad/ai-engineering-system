---
name: nestjs-foundation
description: Use to plan a NestJS foundation after NestJS is approved — module boundaries, dependency injection, providers, pipes/guards/interceptors/filters, config module, conventions for a multi-developer codebase. Plans only; scaffolds nothing without task approval.
---

# NestJS Foundation

## Purpose

Define the foundation of an approved NestJS API: module boundaries per domain, DI/provider conventions, the request lifecycle (pipes → guards → interceptors → filters), and configuration — so the framework's structure serves the domains rather than ceremony.

## When to Use

- After `backend-stack-selection` chose NestJS and Gate 2 approved it.
- **Not** for Express (`express-foundation`) or before the stack decision.

## Inputs

- Approved stack decision and architecture (`../../architecture-design`).
- Domain list, team conventions, async/realtime needs (queues, WebSockets, schedules).

## Discovery Questions

- What are the domains, and which share providers?
- Which cross-cutting concerns are global (validation pipe, auth guard, exception filter)?
- Are queues/WebSockets/scheduling in scope now (Nest modules for each)?

## Responsibilities

- Define **modules per domain**, each owning its controllers, services, and data providers; shared code in explicit shared modules — no circular imports.
- Set the request lifecycle: global **ValidationPipe** (whitelist, transform), **guards** for authn/authz, **interceptors** for logging/serialization, **exception filters** mapping to the error contract (`backend-error-handling`).
- Plan `ConfigModule` with validated env schema (fail fast).
- Note where Nest's ecosystem covers approved needs (queues, WebSocket gateways, schedule module) — each still a justified choice.
- Keep providers injectable and testable; no service locators or hidden singletons.

## Required Workflow

1. Confirm approval + architecture inputs.
2. Map domains → modules; mark shared modules explicitly.
3. Define global pipes/guards/interceptors/filters and their order.
4. Define config + env validation.
5. Record the foundation plan; implementation follows approved tasks only.

## Decision Rules

- One module per domain; split only when a module grows multiple concerns.
- Global validation pipe with `whitelist: true` — unknown fields never reach services.
- Authorization is a guard + service-level check, not controller `if`s (`backend-authorization`).
- Prefer Nest-native integrations for queues/WebSockets/scheduling when those needs are approved — coherence is why NestJS was chosen.

## Rules

- No scaffolding or dependency installation before task approval (Gate 4).
- Module boundaries mirror the architecture's domain boundaries.
- Every global concern (pipe/guard/filter) recorded — they are invisible in handlers.

## Anti-Patterns

- One giant `AppModule` with everything registered globally.
- Circular module imports patched with `forwardRef` instead of fixing boundaries.
- Business logic in controllers or guards.
- Adopting every Nest feature (CQRS, microservices) without a requirement.

## Validation Checklist

- [ ] Domain → module map defined; shared modules explicit; no cycles.
- [ ] Global pipes/guards/interceptors/filters planned and ordered.
- [ ] Config module + env validation planned (fail fast).
- [ ] Async/realtime module needs noted against approvals.
- [ ] Nothing installed or scaffolded yet.

## Definition of Done

A recorded NestJS foundation plan — module map, request lifecycle, config, conventions — ready to drive approved implementation tasks for a multi-developer codebase.

## Related Skills

`backend-stack-selection`, `backend-api-architecture`, `backend-validation`, `backend-error-handling`, `backend-authorization`, `queues`, `realtime-communication`, `scheduled-jobs`.

## Related Knowledge

`../../../knowledge/` (domain boundaries, team conventions).

## Related References

`../../../references/backend/nestjs/` (module examples, when populated).

## Context Loading Guidance

- **Requires:** approved stack decision, domain list, architecture summary.
- **Does not require:** database internals, other packs, unrelated references.
- **May load:** `backend-api-architecture`, `backend-authorization` (guard design).
- **Stop when:** the foundation plan is recorded.

## Token Efficiency Guidance

Plan at module-map altitude; a domain→module table beats generated code. Delegate lifecycle details to the specialist skills.
