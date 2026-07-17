---
name: backend-stack-selection
description: Use to decide between Express and NestJS (or flag another fit) for a backend API, with justification. Evaluates Express (small/medium APIs, lightweight architecture, fast delivery, limited modules) against NestJS (large modular systems, multiple developers, dependency injection, queues, WebSockets, many domains, strong conventions). No universal default.
---

# Backend Stack Selection

## Purpose

Choose the backend framework — **Express** or **NestJS** — for the API's real requirements, with a justified recommendation. Feeds `../../stack-recommendation` (backend area) and the matching foundation skill. Requires user approval before implementation (Gate 2).

## When to Use

- At the start of any backend project, or when adding a backend service.
- When re-evaluating an existing backend's framework for a migration.
- **Not** after the framework is approved and unchanged.

## Inputs

- Requirement baseline (`../../requirements-analysis`) and selected applications (`../../application-selection`).
- Existing backend findings (`existing-backend-audit`) if any.
- Domain count, team size, async/realtime needs, expected lifespan and growth.

## Discovery Questions

- How many distinct domains/modules will the API have, now and in a year?
- How many developers will work on it concurrently?
- Are queues, WebSockets, scheduled jobs, or microservices in scope?
- Is fast initial delivery or long-term structural consistency the priority?
- Does the team already know one framework well?

## Responsibilities

- Evaluate **Express**: small/medium APIs, lightweight architecture, fast delivery, limited module count, freedom to shape structure (paired with `backend-api-architecture` so it stays layered).
- Evaluate **NestJS**: large modular systems, multiple developers, dependency injection, first-class queues/WebSockets/scheduling, many domains, strong conventions that keep teams consistent.
- Recommend one framework with justification and the trade-offs of the other.
- Hand off to `express-foundation` or `nestjs-foundation`.

## Required Workflow

1. Gather domain count, team size, async/realtime needs, delivery constraints.
2. Score both frameworks against those requirements — not against preference.
3. Recommend one, with trade-offs and the conditions that would flip the choice.
4. Record the recommendation for Gate 2 approval.
5. Hand off to the matching foundation skill.

## Decision Rules

- **No universal default.** Evaluate the requirements; either framework can be the right answer.
- Lean **Express** when: few modules, small team, straightforward CRUD/API surface, speed of delivery dominates, and heavyweight structure would be overhead.
- Lean **NestJS** when: many domains/modules, multiple developers needing shared conventions, dependency injection aids testability, or queues/WebSockets/scheduling are core (its ecosystem covers them coherently).
- Team familiarity is a legitimate tiebreaker — not a trump card over structural mismatch.
- Express + discipline can serve growing systems; NestJS on a 3-endpoint service is ceremony. Match weight to load.

## Rules

- Recommend, don't pre-select; the user approves at Gate 2.
- No dependency installation or scaffolding here.
- Justify against actual requirements; record what would change the answer.

## Anti-Patterns

- Defaulting to either framework "because we always use it."
- Choosing NestJS for a tiny API to be "future-proof" with no growth evidence.
- Choosing Express for a many-domain, many-developer system and reinventing DI/module conventions ad hoc.
- Deciding before domain count and team shape are known.

## Validation Checklist

- [ ] Domains, team size, async/realtime needs gathered.
- [ ] Express path evaluated (lightweight, fast delivery, limited modules).
- [ ] NestJS path evaluated (modularity, DI, queues, WebSockets, conventions).
- [ ] One framework recommended with justification + trade-offs.
- [ ] No universal default applied.
- [ ] Recorded for Gate 2.

## Definition of Done

A recorded, justified Express-vs-NestJS recommendation tied to the API's real requirements, with trade-offs noted and approval pending — ready to hand to the matching foundation skill.

## Related Skills

`../../stack-recommendation`, `../../application-selection`, `express-foundation`, `nestjs-foundation`, `existing-backend-audit`, `backend-api-architecture`, `queues`, `realtime-communication`.

## Related Knowledge

`../../../knowledge/` (team constraints, existing services, growth expectations).

## Related References

`../../../references/backend/` (framework comparison notes, when populated).

## Context Loading Guidance

- **Requires:** requirement baseline, selected apps, domain/team/async summary.
- **Does not require:** the full backend skill set, app source, unrelated references.
- **May load:** `existing-backend-audit` (existing service), one foundation skill after the decision.
- **Stop when:** the framework recommendation is recorded for Gate 2.

## Token Efficiency Guidance

Decide from the requirements summary. Keep the recommendation to the decisive factors (domains, team, async needs); link references rather than pasting framework docs.
