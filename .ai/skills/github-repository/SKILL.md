---
name: github-repository
description: Use to plan and (only with explicit approval) perform GitHub repository operations — creation, visibility, remotes, topics, PRs, protections. Repository creation and any outward-facing action require explicit user approval and least-privilege tool access.
---

# GitHub Repository

## Purpose

Handle GitHub-side repository operations safely: creation, visibility, remotes, topics, PRs, and protections — always under explicit approval for outward-facing actions. Complements local `git-workflow`.

## When to Use

- When a repo must be created, configured, or connected to a remote — **with approval**.
- When managing PRs/issues/protections on an existing repo.
- **Not** proactively; and **never** create a repo without explicit approval.

## Inputs

- The action requested and the target owner/repo.
- Desired visibility (public/private) and settings.
- Tool/permission scope (`../../mcp/`).

## Discovery Questions

- Has the user explicitly approved repository creation / this remote action?
- Public or private?
- Owner/organization and naming?
- What settings (topics, default branch, protections)?

## Responsibilities

- **Create repos only with explicit approval**, at the requested visibility.
- Configure **remote, default branch, topics, description**.
- Manage **PRs/issues** for approved work.
- Apply **branch protections** when requested.
- Use **least-privilege** tool access (`../../mcp/PERMISSION_RULES.md`).

## Required Workflow

1. Confirm explicit approval for any outward-facing action.
2. Confirm visibility, owner, and naming.
3. Perform the action at minimal scope.
4. Set default branch, description, topics as requested.
5. Verify the result (visibility, branch, remote) and report.

## Decision Rules

- Repository creation is outward-facing → requires explicit, task-specific approval.
- Default to the visibility the user specifies; if unspecified for creation, ask.
- No pushing secrets; respect secret-scanning/push-protection (fix, don't bypass).
- Prefer read-only GitHub access unless a write action is approved.

## Rules

- Never create a remote repo without explicit approval (`../../system/OPERATING_RULES.md`).
- Least-privilege, task-scoped tool access; disable after.
- Verify and report the actual resulting state.

## Anti-Patterns

- Creating a repo speculatively or without approval.
- Bypassing push protection to force a secret through.
- Leaving broad GitHub write scope enabled across tasks.
- Assuming visibility instead of confirming.

## Validation Checklist

- [ ] Explicit approval obtained for outward-facing actions.
- [ ] Visibility/owner/naming confirmed.
- [ ] Least-privilege scope used.
- [ ] Default branch/topics/description set as requested.
- [ ] No secrets pushed; protections respected.
- [ ] Resulting state verified and reported.

## Definition of Done

The approved GitHub operation is complete and verified (correct visibility, branch, remote, settings), performed at least privilege, with no secrets pushed and the result reported.

## Related Skills

`git-workflow`, `release-planning`, `security-review`, `project-orchestrator`.

## Related Knowledge

`../../memory/` (owner/org conventions), `../../mcp/` (GitHub tool rules).

## Related References

`../../mcp/RECOMMENDED_SERVERS.md` (GitHub entry).

## Context Loading Guidance

- **Requires:** the approved action, target/visibility, tool scope.
- **Does not require:** application source, references, planning skills.
- **May load:** `../../mcp/` permission rules; `git-workflow` for local side.
- **Stop when:** the action is done and verified, or approval is pending.

## Token Efficiency Guidance

Operate on the specific action and its parameters. Keep verification to the few fields that matter (visibility, branch, remote, topics).
