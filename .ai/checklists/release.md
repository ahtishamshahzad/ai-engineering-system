# Checklist: Release

> Verifiable items for release readiness. Maps to Gate 7, `../hooks/before-release.md`, `../skills/release-planning`. Evidence-based; shipping requires explicit approval.

## Pass Criteria (evidence per line)

- [ ] Gates 1–6 passed and recorded.
- [ ] Final quality audit aggregated; no unresolved critical/high (`../skills/final-quality-audit`).
- [ ] Tests green (or unrun explicitly flagged); required cases covered; smoke path ready.
- [ ] Security & quality review clear.
- [ ] Config validated per environment; secrets in store; none in bundles.
- [ ] Migrations rehearsed; backups + tested restore; rollback rehearsed.
- [ ] Monitoring/alerts live; incident readiness active.
- [ ] Docs + changelog updated; version handled; git workflow followed.
- [ ] **Publish/deploy explicitly approved by the user** — never assumed.

## Decision

- [ ] **GO / NO-GO** recorded. If NO-GO, blockers listed with owners.

## Fail / Stop

- Any gate unmet; unresolved critical/high; readiness unverified; no explicit approval to ship.

## Related

Template: `../templates/RELEASE_CHECKLIST.md`, `FINAL_AUDIT.md`. Skill: `../skills/release-planning`.
