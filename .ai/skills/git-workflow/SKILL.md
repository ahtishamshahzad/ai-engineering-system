---
name: git-workflow
description: Use to plan and perform branching, commits, and PRs safely. Branches off the default branch, never commits/pushes without being asked, keeps secrets out of history, and gates git actions behind the relevant hooks and quality gates.
---

# Git Workflow

## Purpose

Manage version control safely and consistently. Implements `../../system/GIT_WORKFLOW_RULES.md`. Covers branching, commits, and PRs; remote/publish actions require approval.

## When to Use

- When a change is ready to branch/commit/PR (on request).
- When defining the git strategy for a work item/phase.
- **Not** to commit/push proactively without being asked.

## Inputs

- The change to be versioned and its work item.
- Repo conventions (commit style, branch naming).
- The user's authorship/co-author preference.

## Discovery Questions

- Has the user asked to commit/push, or only to implement?
- What is the branch naming and commit convention here?
- Does the repo have hooks/gates to run first?
- Any authorship/co-author trailer preference?

## Responsibilities

- **Branch off the default branch** with a descriptive name.
- Make **small, coherent commits** with why-focused messages.
- Keep **secrets out of commits**; respect `.gitignore`.
- Run **`before-commit` / `before-pr`** hook checks (tests, security).
- Open PRs **only when asked**, with a clear description.
- Follow the user's **authorship preference** (no unrequested trailers).

## Required Workflow

1. Confirm the user wants git actions (not just implementation).
2. Branch from the up-to-date default branch.
3. Stage a coherent change; run before-commit checks.
4. Commit with a clear message.
5. For a PR: run before-pr checks; open with a summary of what/why/testing/risks.

## Decision Rules

- Never commit directly to `main`/`master` — branch first.
- Never commit/push unless asked.
- No remote repo creation here (that's `github-repository`, and requires approval).
- Destructive/history-rewriting ops require explicit confirmation and a reason.

## Rules

- No secrets in commits; flag any previously committed secret for rotation.
- Match repo commit/branch conventions.
- Gate git actions behind hooks/gates (`../../system/QUALITY_GATES.md`).

## Anti-Patterns

- Auto-committing/pushing without request.
- Committing to the default branch.
- Force-push/history rewrite without explicit approval.
- Adding co-author trailers the user didn't ask for.

## Validation Checklist

- [ ] User requested the git action.
- [ ] Branched off the default branch.
- [ ] Commit small/coherent; message explains why.
- [ ] No secrets committed.
- [ ] before-commit/before-pr checks run.
- [ ] Authorship preference respected.

## Definition of Done

Changes are on a descriptive branch with clean, coherent commits (no secrets, correct authorship), pre-commit/pre-PR checks run, and a PR opened only if requested — all consistent with the repo's conventions.

## Related Skills

`github-repository`, `release-planning`, `code-review`, `security-review`, `project-orchestrator`.

## Related Knowledge

`../../memory/` (user git preferences), `../../knowledge/`.

## Related References

None typically.

## Context Loading Guidance

- **Requires:** the change, repo conventions, the user's git preference.
- **Does not require:** unrelated source, references, planning skills' bodies.
- **May load:** `github-repository` (remote), `release-planning` (release).
- **Stop when:** the branch/commit/PR action is complete or a gate blocks it.

## Token Efficiency Guidance

Operate on the diff and conventions only. Keep commit/PR text tight and informative.
