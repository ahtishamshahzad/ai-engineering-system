---
name: repository-strategy
description: Use to decide how a project's applications are organized into repositories — single repo, monorepo, or multiple repos — based on shared code, coupling, team boundaries, and release cadence. A decision, not a default; feeds monorepo-selection when a monorepo is chosen.
---

# Repository Strategy

## Purpose

Decide the **repository shape** for the project's applications — one app repo, a monorepo of several, or separate repos — from real coupling and team factors, so the layout serves development instead of fighting it. Complements the core `../../repository-architecture` (on-disk layout) with the repo-boundary decision.

## When to Use

- After applications are selected (`../../application-selection`), before scaffolding structure.
- When restructuring how existing apps are split across repos.
- **Not** after the layout is decided and stable.

## Inputs

- Selected applications (web, dashboard, mobile, backend, shared packages).
- Shared-code needs, team boundaries, release cadences, existing repos.

## Discovery Questions

- How many applications, and how much code do they genuinely **share** (types, contracts, utilities)?
- Are they **released together** or independently (cadence coupling)?
- Who owns each — one team or several with separate workflows?
- Is there existing tooling/CI that favors one shape?

## Responsibilities

- Evaluate the options against coupling, not fashion:
  - **Single repo (one app)** — one application, no sharing: simplest, default when there's genuinely one thing.
  - **Monorepo (multiple apps + shared packages)** — apps that **share code** (API contracts/types between backend and clients), release in coordination, or benefit from one CI/tooling surface; needs a workspace tool (`monorepo-selection`).
  - **Multiple repos** — apps with **independent ownership, cadence, and little shared code**, or hard access/security boundaries.
- Weigh trade-offs explicitly: monorepo eases sharing + atomic cross-app changes but needs tooling discipline (caching, affected-only CI); multi-repo isolates but duplicates config and complicates shared-contract changes.
- Record the decision and what would flip it; hand off to `monorepo-selection` if monorepo, else to `../../repository-architecture` for the single/multi layout.

## Required Workflow

1. Inventory apps, shared code, cadences, ownership.
2. Match against single / monorepo / multi-repo.
3. Record the decision + trade-offs + flip conditions (Gate 2 where it affects stack/tooling).
4. Hand off to `monorepo-selection` or `../../repository-architecture`.

## Decision Rules

- Shared code + coordinated release + shared tooling → monorepo; independent everything → separate repos; one app → one repo.
- Don't adopt a monorepo (and its tooling cost) for a single app "to be ready."
- Don't split tightly-coupled apps that share contracts into separate repos — cross-repo contract changes become painful.
- Team ownership boundaries are a legitimate reason to split even shared-code apps — record it.

## Rules

- The decision is recorded and tied to the app inventory.
- Repository/tooling choices that affect the stack need approval (`../../stack-recommendation`, Gate 2).
- No scaffolding here — decision only.

## Anti-Patterns

- Monorepo-by-default for one application.
- Multi-repo for apps that share an evolving API contract.
- Choosing the shape before knowing the apps and their coupling.
- Copying another project's repo shape without checking fit.

## Validation Checklist

- [ ] Apps, shared code, cadences, ownership inventoried.
- [ ] Option chosen against real coupling.
- [ ] Trade-offs + flip conditions recorded.
- [ ] Handed to `monorepo-selection` or `../../repository-architecture`.

## Definition of Done

A recorded repository-shape decision — single, monorepo, or multi-repo — justified by coupling, cadence, and ownership, with trade-offs noted and the next skill selected.

## Related Skills

`monorepo-selection`, `../../repository-architecture`, `../../application-selection`, `../../stack-recommendation`, `turborepo-foundation`, `pnpm-workspaces`, `npm-workspaces`, `ci-cd`.

## Related Knowledge

`../../../knowledge/` (team boundaries, release cadences).

## Related References

`../../../references/devops/` (repo-shape notes, when populated).

## Context Loading Guidance

- **Requires:** app inventory, shared-code + ownership + cadence summary.
- **Does not require:** tooling internals, app source.
- **May load:** `monorepo-selection` (if monorepo), `../../repository-architecture`.
- **Stop when:** the repo-shape decision is recorded.

## Token Efficiency Guidance

The app × (shared code, cadence, owner) table drives the decision; keep it to the deciding factors.
