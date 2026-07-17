# Checklist: Deployment

> Verifiable items for a deployment. Maps to `../hooks/before-release.md` / `after-release.md` and Gate 7. Deploys are approval-gated.

## Pre-Deploy

- [ ] Production-readiness verified with **evidence** (not merely planned): config/secrets validated, monitoring/alerts live.
- [ ] Migrations rehearsed; **backups + tested restore** in place; migration ordered before new code serves traffic.
- [ ] **Rollback rehearsed**; trigger conditions known; incident readiness active.
- [ ] One immutable artifact promoted with config injected (no per-environment rebuild; no baked secrets).
- [ ] CI reflects only the applications that exist; security checks passed.
- [ ] **Deploy explicitly approved by the user** — never assumed.

## Post-Deploy

- [ ] **Post-deploy smoke** run against the real environment — app alive; config/secrets/connectivity work.
- [ ] Monitoring healthy (error/latency within bounds); rollback still available.
- [ ] Version tagged; release state recorded.

## Fail / Stop

- Readiness unverified; no tested restore/rehearsed rollback; deploy unapproved; **post-deploy smoke fails → roll back**.

## Related

Skills: `../skills/devops/production-readiness`, `deployment-selection`, `rollback-planning`. Reference: `../references/deployment/`.
