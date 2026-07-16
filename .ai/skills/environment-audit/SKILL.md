---
name: environment-audit
description: Use to assess environment and configuration health — env-var usage and fail-fast, config/secrets handling, build/runtime settings, and parity across dev/staging/prod. Flags exposure and missing validation without printing secret values.
---

# Environment Audit

## Purpose

Assess how the project handles environment configuration and secrets: whether required variables are validated, secrets stay server-side, debug is off in production, and environments are consistent — so deploys don't fail or leak.

## When to Use

- During an existing-project audit, before deployment, or in a security pass.
- When diagnosing config-related failures or missing-variable errors.
- **Not** to print or handle real secret values.

## Inputs

- Config files, `.env.example`, `.gitignore`, build/deploy config (read-only).
- Target environments (dev/staging/prod) and their differences.

## Discovery Questions

- Which env vars are required, and does the app fail fast if they're missing?
- Are secrets server-side only; is `.env` git-ignored; is `.env.example` present with placeholders?
- Is debug/verbose mode off in production?
- Is there parity across environments, or drift?

## Responsibilities

- Verify **required env vars** are referenced and **fail-fast** on absence.
- Verify **secret handling**: server-side only, `.env` ignored, `.env.example` with placeholders, no client-exposed prefixes holding secrets.
- Check **debug/runtime settings** default safe in production.
- Assess **environment parity** and flag drift.
- Flag exposure without printing secret values (`../../system/SECURITY_RULES.md`).

## Required Workflow

1. Read config, `.env.example`, `.gitignore`, build/deploy files.
2. Cross-check referenced vars vs documented vars.
3. Verify fail-fast on missing critical vars.
4. Check secret placement and client exposure (redacted).
5. Check debug defaults and environment parity.
6. Record findings and gaps.

## Decision Rules

- Missing fail-fast on a critical var is a High finding.
- Any secret with a client-exposed prefix is a Critical finding → rotate + move server-side.
- Debug on in production is a finding.
- Prefer parity; document intentional per-environment differences.

## Rules

- Read-only; never print secret values (report location/type redacted).
- Recommend a placeholder `.env.example` if missing.
- Coordinate deeper secret analysis with `security-review`.

## Anti-Patterns

- Printing real secret values.
- Ignoring missing env validation.
- Assuming parity without checking.
- Treating client-exposed secrets as acceptable.

## Validation Checklist

- [ ] Required vars referenced + fail-fast verified.
- [ ] Secrets server-side; `.env` ignored; `.env.example` present.
- [ ] No client-exposed prefix holds a secret.
- [ ] Debug defaults safe in production.
- [ ] Environment parity assessed; drift flagged.
- [ ] No secret values printed.

## Definition of Done

An environment/config report: env-var validation status, secret-handling posture (redacted), production debug settings, and parity/drift findings — with justified recommendations and no secret values printed.

## Related Skills

`existing-project-audit`, `dependency-audit`, `security-review`, `release-planning`, `migration-planning`, `final-quality-audit`.

## Related Knowledge

`../../knowledge/` (deploy targets), `../../mcp/` (tool exposure).

## Related References

`../../mcp/PERMISSION_RULES.md` (data exposure).

## Context Loading Guidance

- **Requires:** config files, `.env.example`, `.gitignore`, build/deploy config, target envs.
- **Does not require:** full application source, references, planning skills' bodies.
- **May load:** `security-review` for deep secret analysis.
- **Stop when:** the environment report is recorded.

## Token Efficiency Guidance

Focus on config and deploy files, not app logic. Redact and quote only what's needed to locate a finding.
