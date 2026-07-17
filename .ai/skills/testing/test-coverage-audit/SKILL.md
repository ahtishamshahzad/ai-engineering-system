---
name: test-coverage-audit
description: Use to assess test coverage by risk, not by percentage — find untested critical paths (auth, money, data integrity, error/denial paths) and missing required cases, and recommend targeted tests. Never chase a coverage number without risk justification.
---

# Test Coverage Audit

## Purpose

Judge whether the *right things* are tested — critical paths, error and authorization-denial branches, the required cases — rather than whether a coverage percentage is high. Coverage % is a hint, never the goal.

## When to Use

- Before release, or when assessing an existing suite's adequacy (`../../existing-project-audit`).
- When deciding where to invest test effort next.
- **Not** to enforce a blanket coverage threshold (that's the anti-pattern this skill guards against).

## Inputs

- The application's critical paths + risk map (`testing-selection`, `../../../knowledge/`).
- The existing suites + optional coverage data (as a hint, not a verdict).

## Discovery Questions

- What are the highest-risk paths (auth, payments, data integrity, permissions) — and are they tested, including their failure/denial branches?
- For key behaviors, are the **required cases** present: happy path, invalid input, error path, authorization denial, regression?
- Where does high line-coverage hide untested branches (error handling, edge cases)?

## Responsibilities

- Map **coverage to risk**: identify critical paths and check each is tested at an appropriate level (`testing-selection` pyramid) — a well-tested trivial util and an untested payment path can share the same coverage %.
- Check the **required-case set** on important behaviors: **happy path, invalid input, error path, authorization denial, regression** — flag missing ones (especially error/denial branches, which line-coverage tools count as covered when only the happy branch ran).
- Treat coverage numbers as a **diagnostic**: low coverage on a critical module is a flag; high coverage is not proof (assertion-free or happy-path-only tests inflate it).
- Recommend **targeted tests** for the gaps that matter, prioritized by risk — not a sweep to hit a number.
- Feed findings to `testing-selection` (missing levels) and the per-type skills (write the gap tests); regression gaps to `regression-testing`.

## Required Workflow

1. Build/confirm the critical-path + risk map.
2. Check each critical path is tested at the right level.
3. Audit required-case completeness on key behaviors (esp. error/denial).
4. Use coverage data as a hint to find blind spots.
5. Recommend prioritized, risk-justified tests; hand to the per-type skills.

## Decision Rules

- **Never chase a coverage percentage without risk justification** — 100% on plumbing while auth is untested is failure dressed as success.
- Error and authorization-denial branches are the usual blind spots — check them explicitly.
- High coverage with weak assertions is worse than honest gaps — inspect test quality, not just the number.
- Prioritize gap-filling by blast radius (money, auth, data loss) first.

## Rules

- Recommendations are risk-justified, not threshold-driven.
- Coverage tools inform; they don't decide adequacy.
- Missing required cases on critical paths are flagged as defects.

## Anti-Patterns

- Mandating "80% coverage" as the quality bar.
- Counting happy-path-only tests as covering a behavior.
- Trusting high line-coverage that skips error/denial branches.
- Writing low-value tests to raise a number.
- Auditing coverage without a risk map.

## Validation Checklist

- [ ] Critical-path + risk map built.
- [ ] Each critical path tested at the right level.
- [ ] Required-case completeness checked (esp. error/denial).
- [ ] Coverage data used as a hint, not a verdict.
- [ ] Prioritized, risk-justified gap tests recommended.

## Definition of Done

A risk-based coverage assessment naming the untested critical paths and missing required cases, with prioritized, risk-justified test recommendations — and no coverage-percentage target treated as the goal.

## Related Skills

`testing-selection`, `unit-testing`, `integration-testing`, `api-integration-testing`, `regression-testing`, `../../existing-project-audit`, `../../final-quality-audit`, `../../security/security-regression-testing`.

## Related Knowledge

`../../../knowledge/` (risk map, critical paths).

## Related References

`../../../references/testing/` (coverage-audit notes, when populated).

## Context Loading Guidance

- **Requires:** critical-path/risk map, existing suites, optional coverage data.
- **Does not require:** rewriting tests here (hand to per-type skills), full source.
- **May load:** `testing-selection`, `regression-testing`.
- **Stop when:** risk-based gaps + prioritized recommendations are recorded.

## Token Efficiency Guidance

The critical-path × required-case matrix (tested? gap?) is the artifact; audit the high-risk paths, not every file.
