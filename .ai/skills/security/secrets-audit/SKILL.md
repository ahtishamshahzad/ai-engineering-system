---
name: secrets-audit
description: Use to audit for exposed secrets — scanning code, config, git history, logs, images, and client bundles for credentials/keys/tokens; confirming exposure; and driving rotation (not just deletion). Auditing existing exposure; secrets-management is the design/build skill.
---

# Secrets Audit

## Purpose

Find secrets that have leaked or could leak — in code, config, **git history**, logs, images, and client bundles — confirm real exposure, and drive **rotation** of anything exposed. A committed secret is compromised; deleting it is not enough.

## When to Use

- Auditing an existing codebase for secret exposure, in `../../security-review`, or after a suspected leak.
- **Not** for designing secret storage (`../../devops/secrets-management`) — this finds what's already exposed.

## Inputs

- Repo (including history), build/CI config, deployed images, client bundles.
- The secret store design (`../../devops/secrets-management`) to compare against.

## Discovery Questions

- Are any secrets in the working tree (source, `.env`, config) — or in **git history** even if since removed?
- Do CI logs, error messages, or observability sinks contain secrets (`../../devops/monitoring-logging` redaction)?
- Are secrets baked into Docker image layers or shipped in client bundles (`../../devops/environment-management` public/secret boundary)?
- For each finding: is it a real, live secret, and what's the rotation/revoke path?

## Responsibilities

- **Scan broadly**: source, config files, `.env`s, **entire git history** (removed-but-committed secrets persist), CI/workflow files, Docker layers, and client bundles for credential/key/token patterns and high-entropy strings.
- **Confirm exposure**: distinguish real live secrets from placeholders/examples/rotated-already; assess reach (public repo? shipped bundle? shared image?).
- **Drive rotation, not deletion**: every confirmed exposed secret is **rotated/revoked** — removing it from code leaves the leaked value valid. Coordinate with the owning system (`../../backend/backend-authentication` for tokens, providers for API keys).
- **Prevent recurrence**: recommend secret scanning in CI/pre-commit (`../../devops/ci-cd`, `../../devops/github-actions`), `.gitignore`/`.dockerignore` gaps closed, and migration to the store (`../../devops/secrets-management`).
- **Handle findings safely**: never reproduce the secret value in reports/messages — reference location + type + severity only.

## Required Workflow

1. Scan working tree, git history, CI, images, bundles.
2. Confirm which findings are real, live secrets; assess reach.
3. For each: drive rotation/revocation with the owning system.
4. Close the leak path (ignore rules, store migration).
5. Add CI/pre-commit scanning; record findings (locations, not values).

## Decision Rules

- History counts: a secret ever committed is exposed even if later removed — rotate it.
- Rotation is mandatory for confirmed exposure; deletion alone leaves a valid credential in the wild.
- Anything in a client bundle is public — bundle secrets are always findings.
- Prioritize by reach × privilege: a live admin key in a public repo outranks a rotated dev token.

## Rules

- Never print discovered secret values — location, type, severity only.
- Confirmed exposures are rotated, not just deleted.
- Recurrence prevention (CI scanning) is part of the fix.

## Anti-Patterns

- Deleting a committed secret from HEAD and calling it fixed (history + leaked value remain).
- Scanning only the working tree, ignoring git history.
- Pasting the found secret into the report/ticket.
- Treating bundle-shipped config as safe.
- No CI/pre-commit scanning to stop the next leak.

## Validation Checklist

- [ ] Scanned: working tree, git history, CI, images, bundles.
- [ ] Real exposures confirmed; reach assessed.
- [ ] Each confirmed secret rotated/revoked.
- [ ] Leak paths closed (ignore rules, store migration).
- [ ] CI/pre-commit scanning added; findings recorded without values.

## Definition of Done

A recorded secrets audit — everywhere secrets hide, including git history — with every confirmed exposure rotated/revoked, leak paths closed, and recurring scanning added, and no secret value reproduced in the findings.

## Related Skills

`../../devops/secrets-management`, `dependency-security`, `../../devops/ci-cd`, `../../devops/github-actions`, `../../devops/environment-management`, `../../backend/backend-authentication`, `../../security-review`, `security-regression-testing`.

## Related Knowledge

`../../../knowledge/` (secret inventory, owning systems).

## Related References

`../../../references/security/` (scan patterns, when populated).

## Context Loading Guidance

- **Requires:** repo + history, CI config, images, bundles, store design.
- **Does not require:** app feature logic, the secret values in clear.
- **May load:** `../../devops/secrets-management`, `dependency-security`.
- **Stop when:** exposures are confirmed, rotated, and recurrence-guarded.

## Token Efficiency Guidance

The findings table (location, type, live?, reach, rotation status) is the artifact — never the secret values themselves.
