---
name: flaky-test-audit
description: Use to find, diagnose, and fix flaky tests — nondeterministic passes/failures from timing, shared state, ordering, real time/randomness, or live externals. Fix the root cause; quarantine only temporarily. A trusted suite is the goal, not a green-by-retry one.
---

# Flaky Test Audit

## Purpose

Restore trust in the test suite by eliminating flakiness at its source: identify tests that pass and fail without code changes, diagnose the real cause, and fix it — because a flaky suite gets ignored, then bypassed, then guards nothing.

## When to Use

- When tests fail intermittently, CI needs reruns to go green, or people say "just re-run it."
- Proactively before flakiness normalizes bypassing.
- **Not** for genuinely-failing tests (those are real bugs — `../../bug-investigation`).

## Inputs

- CI history / rerun logs showing intermittent failures.
- The flaky tests + their environment (`test-environment-management`) and data (`test-data-management`).

## Discovery Questions

- Which tests fail without related code changes, and how often?
- Does the failure depend on order, parallelism, timing, or wall-clock/date?
- Does it touch shared state, real time/randomness, or a live external?

## Responsibilities

- **Detect**: mine CI for pass/fail-without-change signals; rank by frequency and by what they guard.
- **Diagnose the root cause class**:
  - **timing** (fixed sleeps, races, missing awaits) → deterministic waits / auto-wait (`playwright-e2e`/`maestro-e2e`);
  - **shared/leaked state** or **order dependence** → per-test isolation (`test-data-management`);
  - **real clock/randomness** → fake timers, seeded randomness;
  - **live externals / network** → fakes at interfaces (`test-environment-management`);
  - **environment nondeterminism** (fonts, viewport, locale) → pin it (`visual-regression`).
- **Fix the cause**, not the symptom: no blanket retries, no `sleep` bumps, no `.skip` as the resolution.
- **Quarantine sparingly and temporarily**: a persistently-flaky test may be isolated from the gate *with a tracked ticket and an owner*, never silently disabled — and fixed or deleted promptly.
- Add guardrails: run tests in random order in CI to surface order dependence; treat new flakiness as a same-day issue.

## Required Workflow

1. Detect flaky tests from CI history; rank by impact.
2. Reproduce (repeat runs, randomized order, parallelism).
3. Diagnose the root-cause class.
4. Fix the cause at the right layer.
5. Quarantine only if unavoidable — ticketed, owned, time-boxed; verify stability post-fix.

## Decision Rules

- Fix the root cause; retries and sleep-bumps hide flakiness and let it spread.
- Randomized-order CI runs are the cheapest way to expose order dependence — enable them.
- Quarantine is a temporary bridge with a ticket, not a resting place; a skipped test guards nothing.
- A flaky test that can't be made deterministic is a candidate for deletion, not permanent retry.

## Rules

- No blanket auto-retries masking flakiness.
- Quarantined tests are tracked, owned, and time-boxed.
- New flakiness is treated as a real defect, not noise.

## Anti-Patterns

- "Just re-run CI" as the standard response.
- Adding `waitForTimeout`/sleeps to stabilize timing.
- `.skip` as the permanent fix.
- Global test retries hiding real races.
- Ignoring order dependence by always running in the same order.

## Validation Checklist

- [ ] Flaky tests detected + ranked from CI history.
- [ ] Root-cause class diagnosed per test.
- [ ] Cause fixed at the right layer (no retry/sleep bandaids).
- [ ] Any quarantine is ticketed, owned, time-boxed.
- [ ] Randomized-order runs enabled; stability verified.

## Definition of Done

Flaky tests identified, root-caused, and fixed at the source — with randomized-order CI guarding against regression and any quarantine tracked and time-boxed — so the suite is trusted again.

## Related Skills

`test-data-management`, `test-environment-management`, `playwright-e2e`, `maestro-e2e`, `visual-regression`, `test-coverage-audit`, `../../bug-investigation`, `../../devops/ci-cd`.

## Related Knowledge

`../../../knowledge/` (known flaky areas, CI history).

## Related References

`../../../references/testing/` (flakiness patterns, when populated).

## Context Loading Guidance

- **Requires:** CI failure history, the flaky tests + their env/data.
- **Does not require:** the whole suite, unrelated passing tests.
- **May load:** `test-data-management`, `test-environment-management`.
- **Stop when:** causes are fixed and the suite is stable under randomized order.

## Token Efficiency Guidance

Rank by impact and work the top offenders; the flaky-test → cause → fix table is the artifact, not a full-suite review.
