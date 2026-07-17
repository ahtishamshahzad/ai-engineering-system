---
name: visual-regression
description: Use to decide whether visual regression testing is warranted and, if so, plan it — baseline snapshots of key screens/components, deterministic rendering, review-gated diffs, and scoped coverage — without turning it into a flaky, noise-generating burden.
---

# Visual Regression

## Purpose

Catch **unintended visual changes** to important UI (layout breaks, style regressions) via baseline-vs-current image diffs — where visual correctness matters enough to justify the maintenance, kept deterministic and low-noise so diffs mean something.

## When to Use

- For design-critical surfaces (design systems, marketing pages, dashboards) where visual regressions are costly and slip past functional tests.
- **Not** by default on every project — evaluate the payoff first; it's high-maintenance and flaky if done carelessly.

## Inputs

- The UI surfaces where visual correctness is business-critical.
- Rendering environment (Playwright screenshots / Storybook / component snapshots) and CI capacity.

## Discovery Questions

- Which screens/components are visually critical enough that a silent regression would hurt?
- Can rendering be made **deterministic** (fixed fonts, disabled animations, stable data, pinned viewport/browser)?
- Who reviews and approves diffs, and where (PR gate)?

## Responsibilities

- **Decide fit first**: adopt only for surfaces where visual correctness is critical and functional tests can't catch the regression — otherwise recommend against (the maintenance/flake cost outweighs the value on most projects).
- Capture **baselines** of the chosen screens/components; store them versioned and reviewed.
- Enforce **determinism**: pinned viewport/device/browser, fixed fonts (`../../mobile/mobile-fonts` parity), disabled animations, frozen clock, stable seed data — nondeterminism is the source of false diffs.
- Gate diffs on **human review**: a visual change surfaces as a diff to approve or reject in the PR (`../../code-review`), with intentional changes updating the baseline deliberately.
- **Scope tightly**: key surfaces only, not every component in every state — breadth multiplies flake and review load.
- Wire into CI with acceptable tolerance thresholds to absorb sub-pixel noise without hiding real changes (`flaky-test-audit`).

## Required Workflow

1. Decide whether visual regression is warranted; record the decision.
2. If yes: pick the critical surfaces; make rendering deterministic.
3. Capture reviewed baselines.
4. Wire CI diffing with review-gated approval and sane thresholds.
5. Keep scope tight; update baselines only on intentional change.

## Decision Rules

- Default to **not** adopting unless a concrete case justifies it — this tool earns its place, it isn't free.
- Determinism before coverage — a flaky visual suite gets ignored, then bypassed.
- Diffs are reviewed by humans; auto-approving defeats the purpose.
- Narrow, high-value surfaces beat broad, noisy coverage.

## Rules

- Baselines are versioned and reviewed like code.
- Intentional visual changes update baselines deliberately, in the same PR.
- Non-adoption is a valid, recorded outcome.

## Anti-Patterns

- Snapshotting every component "for completeness" → diff noise → ignored suite.
- Nondeterministic renders (animations, live data, system fonts) causing false failures.
- Blanket-accepting diffs without review.
- Adopting visual regression reflexively on a project that doesn't need it.
- Treating pixel diffs as functional test replacement.

## Validation Checklist

- [ ] Fit decision recorded (adopt only where justified).
- [ ] Critical surfaces scoped tightly.
- [ ] Deterministic rendering enforced.
- [ ] Reviewed, versioned baselines.
- [ ] Review-gated CI diffing with sane thresholds.

## Definition of Done

Either a recorded decision not to adopt, or a tightly-scoped, deterministic visual-regression setup on the critical surfaces — reviewed baselines, human-gated diffs in CI, low enough noise that a diff means something.

## Related Skills

`playwright-e2e`, `../../mobile/mobile-design-system`, `../../code-review`, `flaky-test-audit`, `../../devops/ci-cd`, `test-coverage-audit`.

## Related Knowledge

`../../../knowledge/` (visually critical surfaces).

## Related References

`../../../references/testing/` (visual-regression setup, when populated).

## Context Loading Guidance

- **Requires:** the visually critical surfaces, rendering environment.
- **Does not require:** functional test internals, the whole component tree.
- **May load:** `playwright-e2e`, `flaky-test-audit`.
- **Stop when:** the fit decision and (if adopted) the scoped setup are recorded.

## Token Efficiency Guidance

Decide fit before designing anything; if adopted, the surface list bounds the work. Don't inventory every component.
