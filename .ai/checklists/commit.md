# Checklist: Commit

> Verifiable items before committing. Maps to `../hooks/before-commit.md` and `../system/GIT_WORKFLOW_RULES.md`.

## Pass Criteria

- [ ] **Committing was requested/authorized** — never commit unasked.
- [ ] **Not on the default branch** — a descriptive feature/fix/refactor/chore branch is used.
- [ ] **No secrets** in the diff (keys/tokens/credentials scanned); `.gitignore` respected.
- [ ] One logical, in-scope change; message explains the **why**.
- [ ] Relevant checks run for what changed (lint/typecheck/tests as available); unrun checks explicitly flagged.
- [ ] Authorship/co-author trailers follow the user's stated preference.

## Fail / Stop

- Secret in the diff (remove; rotate if ever committed).
- On the default branch (branch first).
- Committing not requested; required check failing.

## Related

Rules: `../system/GIT_WORKFLOW_RULES.md`, `SECURITY_RULES.md`. Skill: `../skills/git-workflow`.
