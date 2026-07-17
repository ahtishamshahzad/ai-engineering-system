---
name: rollback-planning
description: Use to plan how to undo a bad deploy fast — previous-artifact redeploy, migration reversibility rulings (roll-forward vs down), feature-flag kill switches, data-safety on revert, clear triggers, and rehearsal. An untested rollback is a hope, not a plan.
---

# Rollback Planning

## Purpose

Make recovery from a bad deploy fast and safe: know exactly how to get back to a good state, what can and can't be reversed, when to pull the trigger — and prove it works by rehearsing it before you need it.

## When to Use

- When planning a deploy (rollback is part of the deploy design), and before production launches.
- **Not** for incident coordination broadly (`incident-readiness`) — this is the deploy-reversal mechanism.

## Inputs

- Deploy mechanism + artifact strategy (`../../backend/backend-deployment`, `docker-foundation`).
- Migration flow (`../../database/database-migrations`) and any feature-flag system.

## Discovery Questions

- How fast can the **previous artifact** be redeployed (immutable, promotable versions make this minutes)?
- Which migrations are **reversible**, and which are roll-forward-only — and what does that mean for reverting code?
- Are risky changes behind **feature flags** that can be switched off without a redeploy?
- What triggers a rollback (smoke fail, error-rate spike, key flow broken), and who decides?

## Responsibilities

- **Code rollback**: redeploy the previous immutable artifact quickly — the promote-across-environments model makes prior versions instantly available (`../../backend/backend-deployment`); health-gate the rollback like any deploy.
- **Migration reversibility rulings**: know per migration whether it's reversible (honest down migration) or roll-forward-only (schema usually is — dropping data can't be undone). Design deploys **expand/contract** so code can roll back against a still-compatible schema (`../../database/database-migrations`); where a schema change is one-way, the plan is fix-forward, stated in advance.
- **Feature flags as fast rollback**: gate risky features so they can be disabled instantly without a redeploy — the fastest "rollback" of all for behavior changes.
- **Data safety on revert**: writes made by the bad version may be incompatible with the old code — assess and plan (drain, migrate, or accept); backups/snapshots (`../../database/backup-recovery`) as the last resort, not the primary plan.
- **Triggers + ownership**: explicit rollback triggers (post-deploy smoke fail, alert thresholds — `../../testing/smoke-testing`, `monitoring-logging`) and who's authorized to call it.
- **Rehearse**: run the rollback in staging (`staging-environment`) — an unrehearsed rollback is discovered broken mid-incident.

## Required Workflow

1. Confirm fast previous-artifact redeploy.
2. Ruling per migration: reversible vs roll-forward-only; design expand/contract for compatibility.
3. Identify feature-flag kill switches for risky changes.
4. Assess data-safety on revert; define the backup fallback.
5. Define triggers + decision owner.
6. Rehearse in staging; record the runbook.

## Decision Rules

- Code rolls back, schema usually rolls forward — design changes so old code survives the new schema during the window.
- Feature flags beat redeploys for reversing behavior — fastest and lowest-risk.
- A rollback that's never been run is not a rollback — rehearse it.
- Restoring from backup is the last resort (data loss between backup and now), never the first plan.

## Rules

- Every significant deploy has a rollback plan before it ships.
- Migration reversibility is ruled explicitly, in advance.
- The rollback path is rehearsed, with triggers and an owner.

## Anti-Patterns

- "We'll figure out rollback if it breaks."
- Destructive migrations deployed with no compatibility window, stranding code rollback.
- Backup-restore as the primary rollback (data loss, slow).
- No defined trigger — arguing about whether to roll back during the outage.
- Rollback path never tested until the real incident.

## Validation Checklist

- [ ] Fast previous-artifact redeploy confirmed.
- [ ] Per-migration reversibility ruled; expand/contract for compatibility.
- [ ] Feature-flag kill switches identified for risky changes.
- [ ] Data-safety-on-revert assessed; backup fallback defined.
- [ ] Triggers + decision owner explicit.
- [ ] Rehearsed in staging; runbook recorded.

## Definition of Done

A rehearsed rollback plan — fast artifact redeploy, explicit migration reversibility rulings with compatibility windows, feature-flag kill switches, data-safety assessment, and clear triggers with an owner — recorded and proven in staging before production deploys.

## Related Skills

`../../backend/backend-deployment`, `../../database/database-migrations`, `../../database/backup-recovery`, `../../database/data-migration`, `staging-environment`, `../../testing/smoke-testing`, `monitoring-logging`, `incident-readiness`, `production-readiness`.

## Related Knowledge

`../../../knowledge/` (deploy mechanism, flag system, migration inventory).

## Related References

`../../../references/devops/` (rollback runbooks, when populated).

## Context Loading Guidance

- **Requires:** deploy/artifact strategy, migration inventory, flag system.
- **Does not require:** broad incident process, app feature code.
- **May load:** `../../database/database-migrations`, `staging-environment`.
- **Stop when:** the rehearsed rollback plan + triggers are recorded.

## Token Efficiency Guidance

The rollback runbook (steps, migration rulings, triggers, owner) is the artifact; keep it executable and short.
