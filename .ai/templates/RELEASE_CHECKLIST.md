# Release Checklist — <version / release name>

> Fill-in template. The Gate 7 release-readiness checklist. Every item is evidence-based ("planned" ≠ "ready"). Remote/publish/deploy requires **explicit user approval** (`../skills/release-planning`, `../hooks/before-release.md`).

- **Version:** <semver> · **Date:** <YYYY-MM-DD> · **Decision:** GO | NO-GO

## Readiness (evidence per line)

- [ ] Gates 1–6 passed and recorded — <link>
- [ ] Tests green (or unrun explicitly flagged); required cases covered — <link>
- [ ] Smoke path defined; post-deploy smoke ready — <>
- [ ] Security & quality review clear; no unresolved critical/high — <link>
- [ ] Config validated per environment; secrets in store; none in bundles — <>
- [ ] Migrations rehearsed; **backups + tested restore** in place — <>
- [ ] Monitoring/alerts live; **rollback rehearsed**; incident readiness active — <>
- [ ] Docs + changelog updated; version handled — <>

## Approval

- **Remote/publish/deploy approved by:** <user> on <date> — **never assumed.**

## Go / No-Go

- **Decision:** <GO/NO-GO> · **Blockers (if NO-GO):** <list>

## Related

- Skill: `../skills/release-planning`. Hooks: `../hooks/before-release.md`, `after-release.md`. Gate 7.
