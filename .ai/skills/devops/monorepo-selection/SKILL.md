---
name: monorepo-selection
description: Use when a monorepo is chosen to pick the workspace/tooling — pnpm/npm workspaces alone vs adding Turborepo for task orchestration and caching — based on app count, build complexity, and CI needs. Evaluates rather than defaulting to the heaviest option.
---

# Monorepo Selection

## Purpose

Choose the **monorepo tooling** once a monorepo is decided (`repository-strategy`): the package manager's workspaces alone, or workspaces **plus** a task orchestrator (Turborepo) for caching and affected-only builds — sized to the repo's actual complexity.

## When to Use

- After `repository-strategy` chose a monorepo.
- When an existing monorepo's tooling is being reconsidered.
- **Not** for single/multi-repo projects, or before the monorepo decision.

## Inputs

- The apps/packages in the monorepo and their build/test tasks.
- CI needs (build times, how often unaffected apps rebuild), team size.

## Discovery Questions

- How many packages/apps, and how interdependent are their build/test tasks?
- Are CI times already hurting from rebuilding everything on every change (caching payoff)?
- Which package manager is in use or preferred (`pnpm` vs `npm`)?

## Responsibilities

- Decide the **workspace manager**: **pnpm workspaces** (efficient disk/install via content-addressable store, strict dependency isolation — strong default for monorepos — `pnpm-workspaces`) vs **npm workspaces** (already present, simpler, fewer moving parts — `npm-workspaces`).
- Decide whether to add **Turborepo** on top: worth it when there are **multiple buildable packages**, dependency graphs, and CI that benefits from **task caching + affected-only runs**; unnecessary overhead for a two-package repo where plain workspace scripts suffice (`turborepo-foundation`).
- Record the decision + trade-offs; start light and add orchestration when build/CI pain is real, not speculative.
- Feed the choice to `ci-cd` (affected-only jobs) and the foundation skills.

## Required Workflow

1. Inventory packages + their build/test tasks + interdependencies.
2. Pick the workspace manager (pnpm vs npm) with reason.
3. Decide Turborepo yes/no on build-graph + CI-caching payoff.
4. Record the decision + trade-offs.
5. Hand off to the foundation skill(s) + `ci-cd`.

## Decision Rules

- pnpm workspaces is the efficient default for new monorepos; npm workspaces is fine when already standardized on npm and simplicity is valued.
- Add Turborepo when multiple buildable packages + CI caching payoff justify it — not reflexively.
- Start with workspaces-only; introduce the orchestrator when rebuild-everything CI actually hurts.
- Tooling that changes install/CI is a decision worth recording (Gate 2 if it shifts the stack).

## Rules

- Decision recorded and tied to the package inventory.
- No installation/scaffolding before approval.
- Keep tooling proportional to repo complexity.

## Anti-Patterns

- Turborepo on a trivial two-package repo.
- Mixing package managers within one monorepo.
- Adopting orchestration for speculative future scale.
- Choosing tooling before knowing the package graph.

## Validation Checklist

- [ ] Packages + tasks + interdependencies inventoried.
- [ ] Workspace manager chosen (pnpm/npm) with reason.
- [ ] Turborepo decision made on real payoff.
- [ ] Trade-offs recorded; foundation skill selected.

## Definition of Done

A recorded monorepo-tooling decision — workspace manager and whether to add Turborepo — sized to the package graph and CI needs, with trade-offs noted and the foundation skill selected.

## Related Skills

`repository-strategy`, `turborepo-foundation`, `pnpm-workspaces`, `npm-workspaces`, `ci-cd`, `../../stack-recommendation`.

## Related Knowledge

`../../../knowledge/` (package graph, CI pain points).

## Related References

`../../../references/devops/` (monorepo tooling notes, when populated).

## Context Loading Guidance

- **Requires:** package inventory + tasks, CI/build pain, package-manager preference.
- **Does not require:** app source, unrelated infra.
- **May load:** one foundation skill after deciding, `ci-cd`.
- **Stop when:** the tooling decision is recorded.

## Token Efficiency Guidance

The package graph + a yes/no on caching payoff decides it; don't survey every tool feature.
