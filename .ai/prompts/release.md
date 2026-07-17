# Prompt: Release

> Tool-neutral starter. Validate readiness and ship under approval.

---

Cut a **release** (`.ai/` canonical; `.ai/workflows/release.md`, `.ai/skills/release-planning`, `final-quality-audit`).

**Version:** <semver>

Do this:
1. Confirm Gates 1–6 passed; aggregate the reviews into a go/no-go (`.ai/templates/FINAL_AUDIT.md`).
2. Verify Gate 7 readiness with evidence — tests green, security/quality clear, docs + changelog updated, version handled, production-readiness + rollback rehearsed → `.ai/templates/RELEASE_CHECKLIST.md`.
3. Produce the **GO / NO-GO** decision.

Follow `.ai/system/GIT_WORKFLOW_RULES.md`. **Publishing/deploying requires explicit user approval — never assumed.** If NO-GO, list blockers with owners and stop. Hooks: `.ai/hooks/before-release.md`, `after-release.md`.
