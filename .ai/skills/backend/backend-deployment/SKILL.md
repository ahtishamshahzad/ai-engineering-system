---
name: backend-deployment
description: Use to plan backend deployment — target selection, environment config/secrets, build artifact (container) discipline, migration-on-deploy ordering, zero-downtime rollout with health checks, worker/scheduler deployment, and rollback. Deploys require approval.
---

# Backend Deployment

## Purpose

Get the backend into environments safely and repeatably: chosen target, disciplined artifacts, migration ordering, health-checked rollout, and a rollback that's been thought through before it's needed. Actual deploys/publishes are outward-facing — explicit approval (`../../release-planning`, Gate 7).

## When to Use

- When planning how/where the backend runs (staging/production) or changing the deploy pipeline.
- **Not** for release go/no-go itself (`../../release-planning`) or store/app publishing.

## Inputs

- Deployment target constraints/preferences (PaaS, containers on cloud, VMs) — a stack decision needing approval.
- Env/secret model (`../../environment-audit`), DB migration flow (`../../database/database-migrations`), async surfaces (`queues`, `scheduled-jobs`).

## Discovery Questions

- What target fits the team's ops capacity (managed PaaS vs orchestrated containers vs VMs)?
- How many environments, and what differs between them (config only, ideally)?
- What's the rollout policy — can the product tolerate seconds of downtime, or is zero-downtime required?
- Do workers/schedulers deploy with the API or separately (`queues` says separately)?

## Responsibilities

- Recommend the **target** on requirements + ops capacity, with trade-offs, for approval.
- **Artifact discipline**: one immutable build (container image or equivalent) promoted across environments — never rebuilt per environment; config/secrets injected at runtime (`backend-security`), validated at boot (fail fast, before traffic).
- **Migration ordering**: migrations run as a deploy step *before* new code serves traffic; expand→migrate→contract for zero-downtime schema change (`../../database/database-migrations` owns the pattern) — old code must survive the new schema during rollout.
- **Rollout**: readiness-gated (only healthy instances get traffic — `backend-observability` checks), rolling/blue-green per target capability; graceful shutdown (drain requests, finish/release jobs — `queues`).
- **Async surfaces**: workers and schedulers as separately deployable processes; deploy order vs migrations considered for them too.
- **Rollback**: previous artifact redeployable in minutes; know which migrations are roll-forward-only and what that means for reverting (usually: fix forward for schema, roll back code).
- **Pipeline**: CI gates (tests, `../../security-review` hooks) before deploy; deploy actions logged; production deploys approved per `../../../system/QUALITY_GATES.md`.

## Required Workflow

1. Recommend target + environment topology; get approval.
2. Define artifact build + promotion flow.
3. Define config/secret injection + boot validation.
4. Sequence migrations vs code rollout (API, workers, schedulers).
5. Define health-gated rollout + graceful shutdown + rollback runbook.
6. Wire CI gates; record the deploy/rollback procedure.

## Decision Rules

- Prefer the most managed target that meets requirements — ops burden is a permanent tax.
- Same artifact in staging and production; environment differences live in config only.
- A migration that old code can't run against blocks zero-downtime rollout — split it (expand/contract) or accept and record downtime.
- Rollback plans that were never rehearsed are hopes; rehearse in staging.

## Rules

- No deploy/publish without explicit approval; staging first.
- Secrets never bake into artifacts or logs.
- Every production deploy has: approver, artifact ID, migration list, rollback step — recorded.

## Anti-Patterns

- Building on the production box / rebuilding per environment.
- Running migrations manually "when someone remembers."
- Deploying new code before its migration (or a contracting migration before old code is gone).
- Killing workers mid-job on deploy (no drain).
- Rollback = "restore yesterday's DB backup" as the primary plan.

## Validation Checklist

- [ ] Target recommended with trade-offs; approved.
- [ ] Immutable artifact promoted across environments.
- [ ] Config validated at boot; secrets runtime-injected.
- [ ] Migration ↔ rollout ordering safe (expand/contract where needed).
- [ ] Health-gated rollout + graceful shutdown incl. workers/schedulers.
- [ ] Rollback runbook exists and was rehearsed.
- [ ] CI gates + approval flow wired.

## Definition of Done

A recorded, approved deployment design — target, artifact flow, config/secret handling, safe migration ordering, health-gated rollout for API and async processes, rehearsed rollback — with production deploys gated on explicit approval.

## Related Skills

`../../release-planning`, `../../environment-audit`, `../../database/database-migrations`, `../../database/backup-recovery`, `backend-observability`, `queues`, `scheduled-jobs`, `backend-security`, `../../github-repository` (CI/CD residence).

## Related Knowledge

`../../../knowledge/` (ops capacity, environment topology, downtime tolerance).

## Related References

`../../../references/backend/deployment/` (runbooks, when populated).

## Context Loading Guidance

- **Requires:** target constraints, env/secret model, migration flow, async inventory.
- **Does not require:** application feature code, endpoint detail.
- **May load:** `../../database/database-migrations` (ordering), `../../release-planning` (gate).
- **Stop when:** the deploy design + rollback runbook are recorded and approvals wired.

## Token Efficiency Guidance

The deploy sequence diagram (build → migrate → roll API → roll workers) plus the rollback runbook are the artifacts; skip cloud-vendor feature tours.
