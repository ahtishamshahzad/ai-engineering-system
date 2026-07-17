---
name: deployment-selection
description: Use to choose deployment targets per application — managed PaaS, container platform, serverless, static/edge hosting, mobile stores — based on app type, ops capacity, scaling, and cost. Evaluates rather than defaulting; a stack decision requiring approval. Does not provision infrastructure.
---

# Deployment Selection

## Purpose

Choose **where each application runs** in production, matched to the app type and the team's real operational capacity — the most managed option that meets requirements — as a recorded, approval-gated decision. Provisioning and rollout mechanics are `../../backend/backend-deployment`.

## When to Use

- After applications and stack are chosen, before building the deploy pipeline.
- When re-evaluating an existing app's hosting.
- **Not** for the rollout procedure (`../../backend/backend-deployment`) or provisioning infra (out of scope — no scaffolding).

## Inputs

- Selected applications and their runtimes (backend API, web/SSR, static site, mobile).
- Ops capacity, scaling/latency needs, budget, existing infrastructure.

## Discovery Questions

- Per app: what is it (long-running backend, SSR web, static frontend, mobile) and what does it need (persistent processes, edge, workers)?
- What is the team's ops capacity — can they run containers/orchestration, or is managed strongly preferred?
- Scaling profile and budget? Existing cloud/platform commitments?

## Responsibilities

- Match each application to candidate targets:
  - **Backend API / workers** → managed PaaS, container platform, or serverless — factoring persistent connections, background workers/queues (`../../backend/queues`), and cold-start tolerance.
  - **Web / SSR** → SSR-capable host / edge platform; **static frontends** → static/CDN/edge hosting.
  - **Mobile** → app stores via the mobile pipeline (`../../mobile/mobile-release`) — not a server host.
  - **Database** → managed database service (pairs with `../../database/backup-recovery`), not self-hosted unless justified.
- Prefer the **most managed option that meets requirements** — ops burden is a permanent cost; justify any move toward self-managed.
- Record decisions with trade-offs (cost, scale, lock-in, ops) for **approval (Gate 2)**; note what would flip each.
- Feed targets to `ci-cd`/`github-actions` (deploy jobs), `docker-foundation` (if containerized), `staging-environment`, and `production-readiness`.

## Required Workflow

1. Classify each app by type + runtime needs.
2. Shortlist targets per app; weigh ops capacity + scale + cost.
3. Recommend the most-managed sufficient option per app with trade-offs.
4. Record for approval; note flip conditions.
5. Hand off to pipeline + readiness skills.

## Decision Rules

- Most managed that meets the need — reach for containers/orchestration only when requirements demand the control.
- Serverless suits spiky/stateless workloads but weighs cold starts and persistent-connection limits (realtime, long jobs) — check against the app's needs.
- Static frontends don't belong on always-on servers; backends with workers don't fit pure static/edge.
- Target changes are stack decisions — approval required; no lock-in without acknowledging it.

## Rules

- Recommend, don't pre-provision; user approves at Gate 2.
- No infrastructure scaffolding here.
- Each app's target justified against its type + ops reality.

## Anti-Patterns

- One target forced onto every app regardless of type.
- Self-managed Kubernetes for a team without ops capacity.
- Serverless for a persistent-connection/long-job workload without weighing limits.
- Static site on an always-on VM; backend workers on a static host.
- Choosing a target before knowing ops capacity.

## Validation Checklist

- [ ] Each app classified by type + runtime needs.
- [ ] Targets shortlisted; ops/scale/cost weighed.
- [ ] Most-managed sufficient option recommended per app with trade-offs.
- [ ] Recorded for Gate 2; flip conditions noted.
- [ ] Handed to pipeline + readiness skills.

## Definition of Done

A recorded per-application deployment-target recommendation — most-managed option that meets each app's needs, with trade-offs and flip conditions — pending approval and feeding the pipeline and readiness skills.

## Related Skills

`../../backend/backend-deployment`, `../../mobile/mobile-release`, `docker-foundation`, `ci-cd`, `github-actions`, `staging-environment`, `production-readiness`, `../../database/backup-recovery`, `../../stack-recommendation`.

## Related Knowledge

`../../../knowledge/` (ops capacity, budget, existing infra).

## Related References

`../../../references/devops/` (target comparison notes, when populated).

## Context Loading Guidance

- **Requires:** app types/runtimes, ops capacity, scale/cost constraints.
- **Does not require:** provisioning detail, app feature code.
- **May load:** `../../backend/backend-deployment`, `staging-environment`.
- **Stop when:** per-app targets are recommended and recorded for approval.

## Token Efficiency Guidance

The app × (target, ops cost, trade-offs) table is the artifact; decide per app against managed-first.
