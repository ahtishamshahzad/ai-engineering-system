---
name: ci-cd
description: Use to design the CI/CD pipeline generated from the project's selected applications and test selection — jobs like install, lint, typecheck, unit, API, Playwright, mobile, build, security checks, deployment. Only generate jobs for applications that exist; deploys are approval-gated. Does not scaffold infrastructure.
---

# CI/CD

## Purpose

Design a CI/CD pipeline that reflects **what the project actually is** — its selected applications and chosen test types — running the right checks in the right order, gating merges and (with approval) deploys. Platform wiring for GitHub is `github-actions`.

## When to Use

- When establishing or revising the pipeline for a project.
- After applications, tests, and deployment target are selected.
- **Not** to scaffold infrastructure, or to add jobs for applications that don't exist.

## Inputs

- **Selected applications** (`../../application-selection`) — the pipeline is generated *from these*.
- **Test selection** (`../../testing/testing-selection`), repo/monorepo shape (`repository-strategy`, `monorepo-selection`), deployment target (`deployment-selection`).

## Discovery Questions

- Which applications exist (backend, web, dashboard, mobile, shared packages)? The pipeline covers exactly these.
- Which test types were selected per app (unit, API/Supertest, Playwright, Maestro)?
- Is this a monorepo where **affected-only** runs and caching apply (`turborepo-foundation`)?
- What is gated at merge vs deploy, and does deploy require approval?

## Responsibilities

- **Generate jobs from the selected applications + test selection**, choosing from: **install**, **lint**, **typecheck**, **unit tests**, **API tests**, **Playwright (web/dashboard E2E)**, **mobile tests (Maestro)**, **build**, **security checks**, **deployment** — including only those an existing application needs.
- **Never create jobs for applications that don't exist**: no Playwright job without a web app, no mobile job without a mobile app, no deploy job without a deployable — absence of an app means absence of its jobs.
- Order for **fast feedback**: cheap/fast checks (lint, typecheck, unit) before slow ones (E2E, build); fail fast; parallelize independent jobs.
- Use **frozen, cached installs** (`pnpm --frozen-lockfile`/`npm ci`); in monorepos, **affected-only** execution + caching (`turborepo-foundation`).
- Wire **security checks** (dependency/secret scanning — `../../security/dependency-security`, `secrets-audit`) as pipeline jobs.
- Set **gates**: required checks block merge; **deployment jobs are approval-gated** and run only after tests + security pass (`../../backend/backend-deployment`, `production-readiness`, `../../release-planning`).
- Provide **environment/config validation** and post-deploy **smoke** as pipeline steps (`environment-management`, `../../testing/smoke-testing`).

## Required Workflow

1. Read the selected applications + test selection + repo shape.
2. Derive the job set per app (only for apps that exist).
3. Order jobs fast→slow; parallelize; frozen/affected installs.
4. Add security-check jobs.
5. Set merge gates; make deploy approval-gated with post-deploy smoke.
6. Hand platform wiring to `github-actions`.

## Decision Rules

- The pipeline is a **function of the selected applications** — derive it, don't template a fixed pipeline onto every project.
- No job for a nonexistent application, ever.
- Fast checks gate before expensive ones; a failing lint shouldn't wait behind E2E.
- Deploys never run unapproved or on red tests/security (`../../release-planning` gate 7).

## Rules

- Jobs map to real applications and selected test types.
- Deployment is approval-gated; no auto-deploy without explicit policy.
- No infrastructure scaffolding — pipeline design only.

## Anti-Patterns

- A fixed one-size pipeline with jobs for apps the project lacks.
- Playwright/mobile jobs with no corresponding app.
- Slow E2E running before cheap lint/typecheck.
- `npm install` (unpinned) in CI instead of `npm ci`.
- Auto-deploying on green without approval or post-deploy smoke.

## Validation Checklist

- [ ] Job set derived from selected applications + test selection.
- [ ] No jobs for nonexistent applications.
- [ ] Fast→slow ordering; parallel; frozen/affected installs.
- [ ] Security-check jobs included.
- [ ] Merge gates set; deploy approval-gated with post-deploy smoke.
- [ ] Platform wiring handed to `github-actions`.

## Definition of Done

A pipeline design generated from the project's real applications and selected tests — correctly ordered, frozen/affected installs, security checks, merge gates, and approval-gated deploys with post-deploy smoke — with no jobs for applications that don't exist.

## Related Skills

`github-actions`, `../../testing/testing-selection`, `../../application-selection`, `repository-strategy`, `monorepo-selection`, `turborepo-foundation`, `deployment-selection`, `production-readiness`, `../../security/dependency-security`, `../../release-planning`.

## Related Knowledge

`../../../knowledge/` (selected apps, deploy target).

## Related References

`../../../references/devops/` (pipeline patterns, when populated).

## Context Loading Guidance

- **Requires:** selected applications, test selection, repo shape, deploy target.
- **Does not require:** infrastructure provisioning, app feature code.
- **May load:** `github-actions`, `deployment-selection`.
- **Stop when:** the app-derived job set + gates are designed.

## Token Efficiency Guidance

The application × job matrix is the artifact — it directly encodes "only jobs for apps that exist." Build it, then wire the platform.
