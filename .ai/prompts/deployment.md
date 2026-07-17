# Prompt: Deployment

> Tool-neutral starter. Deploy safely — approval-gated.

---

Prepare and run a **deployment** (`.ai/` canonical; `.ai/workflows/deployment.md`, `.ai/skills/devops/deployment-selection`, `production-readiness`).

**Target environment:** <staging | production>

Do this:
1. Confirm production-readiness with **evidence** (config/secrets validated, migrations rehearsed, **backups + tested restore**, monitoring/alerts, rollback rehearsed) → `.ai/templates/RELEASE_CHECKLIST.md`.
2. Deploy **one immutable artifact** with config injected; order migrations safely (expand/contract; single database-engineer owner).
3. Run **post-deploy smoke** against the real environment (`.ai/skills/testing/smoke-testing`); watch monitoring.

**The deploy requires explicit user approval** and is never assumed. **Roll back** if post-deploy smoke fails (`.ai/hooks/after-release.md`). Do not provision infrastructure beyond approved scope.
