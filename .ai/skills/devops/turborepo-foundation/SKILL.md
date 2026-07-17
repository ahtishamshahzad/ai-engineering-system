---
name: turborepo-foundation
description: Use to plan a Turborepo setup after it's chosen — task pipeline definition (dependsOn), input/output declarations for correct caching, affected-only execution, and CI integration. Plans configuration; does not scaffold apps.
---

# Turborepo Foundation

## Purpose

Configure Turborepo so its caching and orchestration are **correct and fast**: a task pipeline with accurate dependencies, precise input/output declarations, and affected-only execution wired into CI. Chosen upstream by `monorepo-selection`.

## When to Use

- After `monorepo-selection` chose Turborepo (over a relational package manager on top of pnpm/npm workspaces).
- **Not** for workspaces-only monorepos or single repos.

## Inputs

- The workspace setup (`pnpm-workspaces`/`npm-workspaces`) and package task graph.
- Build/test/lint task definitions per package.

## Discovery Questions

- What tasks exist per package (build, test, lint, typecheck) and how do they depend across packages?
- What are each task's real **inputs** (source globs, config, env) and **outputs** (dist dirs) — the basis for correct caching?
- Does CI want remote caching, and is it shared across the team/CI safely?

## Responsibilities

- Define the **task pipeline**: `dependsOn` relationships (`^build` for upstream builds), so tasks run in dependency order.
- Declare **inputs and outputs precisely per task** — this is where caching correctness lives: missing inputs → stale cache serving wrong results; missing outputs → cache misses. Include config files and relevant env in inputs.
- Enable **affected-only execution** so CI runs only what changed and its dependents (`ci-cd`), and local runs reuse cache.
- Configure **caching**: local by default; **remote cache** only with a deliberate, secured setup (tokens as secrets — `secrets-management`) and awareness that a poisoned/misconfigured cache is a correctness and supply-chain risk.
- Keep task scripts in the packages; Turborepo orchestrates, it doesn't own the logic.

## Required Workflow

1. Enumerate tasks + cross-package dependencies.
2. Define the pipeline with `dependsOn`.
3. Declare accurate inputs/outputs per task (verify cache correctness).
4. Wire affected-only execution into CI.
5. Configure caching (local; remote only if secured); validate no stale-cache correctness bugs.

## Decision Rules

- Caching correctness depends on complete input/output declarations — under-declaring inputs is a silent-wrong-result bug, over-declaring just misses cache.
- Remote caching is opt-in and secured; treat cache artifacts as trusted-build inputs (supply-chain surface — `../../security/dependency-security`).
- Orchestrate; don't relocate build logic into the pipeline config.
- Validate cache hits produce identical results to clean builds before trusting them in CI.

## Rules

- Cache tokens/secrets via the secret store, never committed (`secrets-management`).
- Pipeline changes reviewed — cache correctness is easy to break subtly.
- Turborepo config stays proportional; no orchestration of tasks that don't exist.

## Anti-Patterns

- Under-declared inputs serving stale cache as fresh results.
- Committed remote-cache tokens.
- Build logic migrated into `turbo.json` instead of package scripts.
- Trusting remote cache without validating correctness.
- Configuring pipelines for tasks/packages that aren't there.

## Validation Checklist

- [ ] Task pipeline with correct `dependsOn`.
- [ ] Accurate inputs/outputs per task (cache-correct).
- [ ] Affected-only execution wired to CI.
- [ ] Caching configured; remote (if any) secured; correctness validated.
- [ ] Secrets handled via the store.

## Definition of Done

A Turborepo pipeline with correct task dependencies and precise, validated input/output declarations, affected-only CI execution, and secured caching — orchestrating existing package tasks without owning their logic.

## Related Skills

`monorepo-selection`, `pnpm-workspaces`, `npm-workspaces`, `ci-cd`, `github-actions`, `secrets-management`, `../../security/dependency-security`.

## Related Knowledge

`../../../knowledge/` (task graph, CI needs).

## Related References

`../../../references/devops/` (pipeline patterns, when populated).

## Context Loading Guidance

- **Requires:** workspace setup, per-package task graph with inputs/outputs.
- **Does not require:** app feature code, unrelated infra.
- **May load:** `ci-cd`, `secrets-management` (remote cache).
- **Stop when:** the pipeline + caching + affected-only CI are configured and validated.

## Token Efficiency Guidance

The task × (dependsOn, inputs, outputs) table is the artifact; get those right and the config follows.
