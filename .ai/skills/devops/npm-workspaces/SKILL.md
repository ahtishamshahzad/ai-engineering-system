---
name: npm-workspaces
description: Use to plan an npm-workspaces monorepo — workspace layout, internal package linking, shared-dependency and versioning policy, and lockfile/CI discipline — when npm is the standardized package manager and simplicity is preferred over pnpm.
---

# npm Workspaces

## Purpose

Set up an npm-based monorepo so internal packages link and shared code works, with reproducible installs — the workspace foundation when `monorepo-selection` chose npm (already standardized, simpler than adding pnpm).

## When to Use

- After `monorepo-selection` chose npm workspaces.
- **Not** for pnpm repos (`pnpm-workspaces`) or single repos.

## Inputs

- Package/app inventory and shared packages.
- Node/npm version policy; whether Turborepo orchestrates (`turborepo-foundation`).

## Discovery Questions

- What is the workspace layout (`apps/*`, `packages/*`) and which packages are shared?
- Which internal packages depend on which?
- How are Node/npm versions pinned and the lockfile enforced in CI?

## Responsibilities

- Define the `workspaces` field (package globs) and an `apps/` vs `packages/` layout.
- Link internal packages via workspaces so cross-package imports resolve to local source; use `*`/workspace-range dependencies for internal packages.
- Note npm's **hoisting** behavior: the shared `node_modules` can expose **phantom dependencies** (packages resolvable but not declared) — still declare every dependency each package imports, so it survives extraction/publish.
- Decide **shared-package versioning**: lockstep vs independent publish; record it.
- Enforce **reproducibility**: committed `package-lock.json`, `npm ci` (not `npm install`) in CI, pinned npm/Node via `packageManager`/engines + corepack.
- Coordinate with `docker-foundation` (cached `npm ci` layers) and `ci-cd`.

## Required Workflow

1. Define workspace globs + layout.
2. Wire internal package linking.
3. Enforce explicit dependency declarations (guard against phantom deps).
4. Decide shared-package versioning.
5. Enforce `npm ci` + pinned versions in CI.

## Decision Rules

- Every imported package is declared in that package's `package.json`, regardless of hoisting — phantom deps break on publish/extraction.
- `npm ci` against a committed lockfile in CI; `npm install` in CI drifts.
- Pin npm/Node so installs match across environments.
- If strict isolation or install efficiency becomes a pain point, that's a signal to revisit `monorepo-selection` (pnpm) — record it rather than fighting hoisting.

## Rules

- Lockfile committed; `npm ci` in CI.
- Explicit dependency declarations per package.
- Node/npm versions pinned.

## Anti-Patterns

- Relying on hoisted phantom dependencies.
- `npm install` in CI instead of `npm ci`.
- Uncommitted/unpinned lockfile → non-reproducible installs.
- Flat package layout with unclear boundaries.
- Registry versions for local internal packages.

## Validation Checklist

- [ ] Workspace globs + layout defined.
- [ ] Internal linking wired.
- [ ] All imports explicitly declared per package.
- [ ] Shared-package versioning decided.
- [ ] `npm ci` + pinned versions in CI.

## Definition of Done

An npm-workspace setup with a clear layout, linked internal packages, explicit per-package dependencies, a recorded versioning policy, and reproducible `npm ci` installs with pinned versions.

## Related Skills

`monorepo-selection`, `pnpm-workspaces`, `turborepo-foundation`, `docker-foundation`, `ci-cd`, `../../security/dependency-security`.

## Related Knowledge

`../../../knowledge/` (package graph, version policy).

## Related References

`../../../references/devops/` (workspace patterns, when populated).

## Context Loading Guidance

- **Requires:** package inventory, internal dep graph, version policy.
- **Does not require:** app feature code, unrelated infra.
- **May load:** `turborepo-foundation`, `ci-cd`.
- **Stop when:** layout, linking, dependency discipline, versioning, and lockfile are set.

## Token Efficiency Guidance

The layout + internal-dep list is the artifact; keep policy to declarations and versioning.
