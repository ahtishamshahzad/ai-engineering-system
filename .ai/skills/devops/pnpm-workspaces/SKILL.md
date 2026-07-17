---
name: pnpm-workspaces
description: Use to plan a pnpm-workspaces monorepo — workspace layout, internal package linking (workspace protocol), dependency hoisting/isolation policy, versioning of shared packages, and lockfile/CI discipline. Configuration planning; does not scaffold apps.
---

# pnpm Workspaces

## Purpose

Set up a pnpm-based monorepo so internal packages link cleanly, dependencies stay isolated, and installs are fast and reproducible — the workspace foundation chosen by `monorepo-selection`, with or without Turborepo on top.

## When to Use

- After `monorepo-selection` chose pnpm workspaces.
- **Not** for npm-workspace repos (`npm-workspaces`) or single repos.

## Inputs

- The package/app inventory and which packages are shared (contracts, types, utils).
- Node/pnpm version policy; whether Turborepo orchestrates (`turborepo-foundation`).

## Discovery Questions

- What is the workspace layout (`apps/*`, `packages/*`) and which packages are internal-shared?
- Which internal packages depend on which (via the workspace protocol)?
- What Node/pnpm versions are pinned, and how is the lockfile enforced in CI?

## Responsibilities

- Define `pnpm-workspace.yaml` **package globs** and a clear `apps/` vs `packages/` layout.
- Link internal packages with the **`workspace:` protocol** so cross-package deps resolve to local source, not the registry.
- Set the **dependency policy**: pnpm's strict isolation (no phantom deps) is a feature — each package declares what it imports; avoid loosening hoisting unless a tool genuinely needs it (record why).
- Handle **shared-package versioning**: internal packages either move in lockstep (unversioned/`workspace:*`) or are independently versioned/published — decide and record.
- Enforce **reproducibility**: committed `pnpm-lock.yaml`, `--frozen-lockfile` in CI, pinned pnpm version (via `packageManager` field / corepack).
- Coordinate with `docker-foundation` (workspace-aware, cached installs in images) and `ci-cd` (frozen installs, affected-only with Turborepo).

## Required Workflow

1. Define workspace globs + layout.
2. Wire internal deps via `workspace:` protocol.
3. Set the isolation/hoisting policy (keep strict by default).
4. Decide shared-package versioning.
5. Enforce lockfile + pinned-version discipline in CI.

## Decision Rules

- Keep pnpm's strict isolation — phantom-dependency bugs it prevents are real; loosen only with a recorded reason.
- `workspace:` protocol for all internal deps — never registry versions for local packages.
- `--frozen-lockfile` in CI; a drifting lockfile is a reproducibility bug.
- Pin the package manager version so every environment installs identically.

## Rules

- Lockfile committed and frozen in CI.
- Each package declares its own dependencies (no relying on hoisted phantoms).
- Node/pnpm versions pinned across environments.

## Anti-Patterns

- Registry versions for internal packages instead of `workspace:`.
- Loosening hoisting globally to "fix" a missing declared dependency.
- Uncommitted or unfrozen lockfile → non-reproducible installs.
- Unpinned pnpm version drifting between local and CI.
- Flat, unstructured package layout.

## Validation Checklist

- [ ] Workspace globs + apps/packages layout defined.
- [ ] Internal deps via `workspace:` protocol.
- [ ] Isolation policy set (strict by default; exceptions recorded).
- [ ] Shared-package versioning decided.
- [ ] Frozen lockfile + pinned pnpm in CI.

## Definition of Done

A pnpm-workspace setup with a clear layout, `workspace:`-linked internal packages, strict dependency isolation, a recorded versioning policy, and frozen, pinned, reproducible installs across environments.

## Related Skills

`monorepo-selection`, `turborepo-foundation`, `npm-workspaces`, `docker-foundation`, `ci-cd`, `../../security/dependency-security`.

## Related Knowledge

`../../../knowledge/` (package graph, version policy).

## Related References

`../../../references/devops/` (workspace patterns, when populated).

## Context Loading Guidance

- **Requires:** package inventory, internal dep graph, version policy.
- **Does not require:** app feature code, unrelated infra.
- **May load:** `turborepo-foundation`, `ci-cd`.
- **Stop when:** layout, linking, isolation, versioning, and lockfile discipline are set.

## Token Efficiency Guidance

The layout + internal-dep list is the artifact; keep policy to the isolation and versioning decisions.
