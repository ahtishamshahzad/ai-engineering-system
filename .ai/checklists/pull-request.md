# Checklist: Pull Request

> Verifiable items before opening/merging a PR. Maps to `../hooks/before-pr.md` / `after-pr.md` and Gates 5–6.

## Pass Criteria (before opening)

- [ ] **Gate 5:** required tests pass (or unrun flagged); required cases covered (happy / invalid / error / **authorization denial** / regression).
- [ ] **Gate 6:** code review + security review done; no unresolved critical/high.
- [ ] No secrets anywhere in the branch history.
- [ ] Docs/changelog updated for user-facing changes.
- [ ] If parallel work: outputs **integration-reviewed** as a whole.
- [ ] PR description states what/why, testing, and risks; opening is authorized.

## Before Merge

- [ ] Review feedback addressed or explicitly deferred; CI green.
- [ ] **Merge is authorized** — never merge to the default branch unasked; no force-push.
- [ ] Base moved? Conflicts resolved cleanly; re-verified.

## Fail / Stop

- Failing required test or open critical/high; secret in history; merge not authorized.

## Related

Template: `../templates/PR_DESCRIPTION.md`. Rules: `../system/GIT_WORKFLOW_RULES.md`.
