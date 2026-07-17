# Audit — <subject> (<type>)

> Fill-in template for an audit (existing-project, dependency, environment, security, performance, testing). Read-only findings; changes nothing. Separate **Confirmed** from **Potential**.

- **Date:** <YYYY-MM-DD> · **Type:** existing-project | dependency | environment | security | performance | testing · **Auditor:** <agent/role>

## Scope

<What was audited, and to what depth (deep where the upcoming change lands).>

## Method

<How findings were gathered — files sampled, checks run, evidence basis.>

## Findings

| # | Finding | Confirmed / Potential | Severity | Evidence (file:line / output) | Recommendation |
|---|---------|-----------------------|----------|-------------------------------|----------------|
| 1 | <> | <> | crit/high/med/low | <> | <> |

## Risks / Gaps

- <Notable risk not tied to a single finding>

## Nothing Modified

- [ ] This audit changed no files. Secrets, if found, are referenced by location only and flagged for rotation (never printed).

## Related

- Workflows: `../workflows/security-audit.md`, `performance-audit.md`, `testing-audit.md`. Reports live in `../generated/`.
