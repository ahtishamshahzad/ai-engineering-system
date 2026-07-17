---
name: github-actions
description: Use to implement the designed CI/CD pipeline as GitHub Actions workflows — jobs/matrices, dependency + build caching, pinned action versions, least-privilege GITHUB_TOKEN, secrets from the store, environment protection rules for approval-gated deploys. Implements the ci-cd design; adds no jobs for nonexistent apps.
---

# GitHub Actions

## Purpose

Turn the `ci-cd` pipeline design into secure, efficient GitHub Actions workflows — correct triggers, caching, pinned actions, least-privilege permissions, and environment protection for gated deploys — without introducing jobs the design didn't call for.

## When to Use

- When the CI platform is GitHub and the `ci-cd` design is ready to implement.
- **Not** for designing the pipeline (`ci-cd`) or on non-GitHub platforms.

## Inputs

- The `ci-cd` job design (app-derived jobs + gates).
- Secret store + environments (`secrets-management`, `environment-management`), repo/monorepo shape.

## Discovery Questions

- What triggers each workflow (PR, push to main, tag, manual dispatch)?
- Which jobs need which secrets, and from where (repo/environment secrets)?
- Which deploys need **environment protection rules** (required reviewers) for approval gating?

## Responsibilities

- Implement the **designed jobs** as workflows/jobs with correct `needs` ordering and **matrices** where useful (Node versions, packages); implement exactly the app-derived set from `ci-cd` — no invented jobs for absent apps.
- **Cache** dependencies and build outputs (setup-action caches, Turborepo remote cache if configured — `turborepo-foundation`); frozen installs (`npm ci`/`pnpm --frozen-lockfile`).
- **Pin action versions** (by major tag at least, commit SHA for third-party actions) — floating `@main` is a supply-chain risk (`../../security/dependency-security`).
- Apply **least-privilege `GITHUB_TOKEN`**: default read-only `permissions`, granting `write` scopes only per job that needs them.
- Source **secrets from GitHub secrets / environment secrets** (`secrets-management`) — never inline; never echo into logs; be mindful of untrusted `pull_request_target` and fork PRs (don't expose secrets to fork code).
- Gate deploys with **GitHub Environments + required reviewers** so production deploy jobs pause for approval (`ci-cd`, `../../release-planning`); run **post-deploy smoke** (`../../testing/smoke-testing`).
- Keep workflows DRY (reusable/composite workflows) where the repo has several apps.

## Required Workflow

1. Map the `ci-cd` jobs to workflows + triggers.
2. Implement jobs with `needs` ordering + matrices; frozen installs + caching.
3. Pin all actions; set least-privilege `permissions`.
4. Wire secrets from the store; guard fork/PR secret exposure.
5. Add environment protection rules for gated deploys + post-deploy smoke.

## Decision Rules

- Implement only the designed, app-derived jobs — the platform layer doesn't invent scope.
- Default `permissions: read-all`; escalate per job, never globally to write.
- Pin third-party actions to a SHA; a hijacked floating tag runs arbitrary code with your token/secrets.
- Never expose secrets to untrusted fork PR code (`pull_request_target` care).
- Production deploys pass through an Environment with required reviewers.

## Rules

- Actions pinned; `GITHUB_TOKEN` least-privilege.
- Secrets from the store, never logged or inlined.
- Deploy jobs approval-gated via Environments.

## Anti-Patterns

- Adding workflow jobs for apps the project doesn't have.
- `uses: some/action@main` (unpinned third-party).
- Global `permissions: write-all`.
- Secrets echoed in logs or exposed to fork PRs.
- Auto-deploy to production with no environment approval.

## Validation Checklist

- [ ] Designed jobs implemented (no extras for absent apps).
- [ ] `needs` ordering + matrices; frozen installs + caching.
- [ ] Actions pinned; least-privilege `GITHUB_TOKEN`.
- [ ] Secrets from store; fork/PR exposure guarded.
- [ ] Environment protection for gated deploys + post-deploy smoke.

## Definition of Done

The `ci-cd` design implemented as GitHub Actions workflows — correctly ordered cached jobs, pinned actions, least-privilege token, store-sourced secrets, and environment-protected approval-gated deploys — matching the app-derived job set exactly.

## Related Skills

`ci-cd`, `secrets-management`, `environment-management`, `turborepo-foundation`, `deployment-selection`, `../../testing/smoke-testing`, `../../security/dependency-security`, `../../release-planning`, `../../github-repository`.

## Related Knowledge

`../../../knowledge/` (triggers, environments, reviewers).

## Related References

`../../../references/devops/` (workflow patterns, when populated).

## Context Loading Guidance

- **Requires:** the `ci-cd` job design, secret store + environments, repo shape.
- **Does not require:** re-deciding the pipeline scope, app feature code.
- **May load:** `secrets-management`, `deployment-selection`.
- **Stop when:** workflows implement the design with security + gates in place.

## Token Efficiency Guidance

Work from the `ci-cd` job matrix; the workflow is its implementation. Keep YAML discussion to triggers, permissions, and gates.
