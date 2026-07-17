# Prompt: GitHub Setup

> Tool-neutral starter. Repository/CI setup — creation and remote actions require approval.

---

Set up **GitHub / CI** for the project (`.ai/` canonical; `.ai/skills/github-repository`, `.ai/skills/devops/ci-cd`, `github-actions`).

**Applications selected:** <list> (CI is generated from these)

Do this:
1. Plan the CI/CD pipeline **from the selected applications only** — jobs from {install, lint, typecheck, unit, API, Playwright, mobile, build, security checks, deployment} — **no jobs for apps that don't exist** (`.ai/skills/devops/ci-cd`).
2. Implement as GitHub Actions: pinned actions, least-privilege `GITHUB_TOKEN`, secrets from the store, environment-protected (approval-gated) deploys (`.ai/skills/devops/github-actions`).
3. Wire security checks (dependency + secret scanning).

**Remote repository creation and any push/publish require explicit user approval** (`.ai/system/OPERATING_RULES.md`, `GIT_WORKFLOW_RULES.md`). Do not scaffold infrastructure. Confirm before any remote action.
