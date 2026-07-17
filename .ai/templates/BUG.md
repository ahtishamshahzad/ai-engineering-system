# Bug — <short title>

> Fill-in work-item template. Audit → reproduce → root cause → minimal fix → regression test → validate (`../skills/bug-investigation`).

- **Date:** <YYYY-MM-DD> · **Severity:** critical | high | medium | low · **Status:** open | fixed | verified

## Summary

<What's wrong, observed vs expected.>

## Reproduction

1. <Step>
2. <Step>
- **Environment:** <where it reproduces>
- **Reliably reproducible:** yes | no (if no, investigate first)

## Root Cause

<The actual cause, not the symptom. File:line where relevant.>

## Fix

<The minimal, targeted change. No bundled refactors.>

## Regression Test

- **Level:** unit | integration | api | e2e (lowest that captures it)
- **Fails before fix / passes after:** [ ] confirmed
- **Security regression (if vuln):** <`../skills/security/security-regression-testing`>

## Validation

- [ ] Root cause addressed · [ ] regression test in CI · [ ] no scope creep · [ ] nearby behavior unchanged

## Related

- Workflow: `../workflows/bugfix.md`. Hook: `../hooks/before-bugfix.md`.
