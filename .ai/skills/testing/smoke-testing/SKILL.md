---
name: smoke-testing
description: Use to define a minimal, fast smoke suite that proves a build/deploy is fundamentally alive — app starts, health checks pass, auth works, primary flows load — run after every build and post-deploy as a go/no-go gate, distinct from full regression.
---

# Smoke Testing

## Purpose

Give every build and deploy a fast **is-it-alive** check: a tiny set of flows that must pass or the release is aborted/rolled back. Not comprehensive — a shallow, wide safety net at the moments things break.

## When to Use

- Post-build and **post-deploy** (staging and production) as a go/no-go gate.
- As a quick local/PR sanity check before deeper suites.
- **Not** as a substitute for regression or E2E depth — smoke is deliberately shallow.

## Inputs

- The critical "must work or we're down" flows across apps (`testing-selection`).
- Deploy pipeline + health endpoints (`../../devops/ci-cd`, `../../devops/production-readiness`, `../../backend/backend-observability`).

## Discovery Questions

- What proves each app is fundamentally working (backend health/readiness green, web loads + auth, mobile launches + logs in)?
- What runs post-deploy against the real environment as the final gate?
- What's the abort/rollback trigger if smoke fails (`../../devops/rollback-planning`)?

## Responsibilities

- Define a **minimal cross-app smoke set**: backend liveness/readiness + one authenticated core request; web app loads + login + primary screen; mobile launch + login + core screen (`maestro-e2e` smoke flows).
- Keep it **fast and stable** — seconds to a couple minutes; flakiness here blocks all deploys, so stability is paramount (`flaky-test-audit`).
- Run it at the right moments: after build (artifact sane) and **after deploy against the real environment** (config/secrets/connectivity actually work — the class of failure unit tests can't catch).
- Wire smoke failure to **go/no-go**: block promotion, trigger rollback (`../../devops/rollback-planning`), alert (`../../devops/monitoring-logging`).
- Keep it distinct from regression — resist growing smoke into a full suite.

## Required Workflow

1. Identify the alive-signals per app.
2. Script minimal, stable smoke flows (reuse E2E smoke where present).
3. Run post-build and post-deploy against real environments.
4. Wire failure to abort/rollback + alert.
5. Guard against scope creep; keep it fast.

## Decision Rules

- Shallow and wide: touch each critical surface once, don't test depth.
- Post-deploy smoke against the real environment is the highest-value run — it catches env/config/secret failures nothing else does.
- If smoke gets slow or flaky, it stops gating deploys — protect speed and stability.
- Smoke failure is a stop signal, not a warning.

## Rules

- Smoke stays minimal — depth lives in regression/E2E.
- Post-deploy smoke is mandatory before declaring a deploy healthy.
- Failures gate promotion and can trigger rollback.

## Anti-Patterns

- A "smoke" suite that grew into full regression (slow, flaky, ignored).
- Skipping post-deploy smoke and learning from users that config broke.
- Smoke that only runs pre-deploy (misses environment failures).
- Flaky smoke that gets bypassed "to unblock the deploy."
- No rollback trigger wired to smoke failure.

## Validation Checklist

- [ ] Minimal alive-signals per app defined.
- [ ] Fast, stable smoke flows scripted.
- [ ] Runs post-build **and** post-deploy against real environments.
- [ ] Failure wired to abort/rollback + alert.
- [ ] Kept distinct from regression; scope creep resisted.

## Definition of Done

A fast, stable smoke suite proving each app is alive, run post-build and post-deploy against real environments, wired to go/no-go with rollback on failure — and deliberately kept minimal.

## Related Skills

`maestro-e2e`, `playwright-e2e`, `regression-testing`, `flaky-test-audit`, `../../devops/ci-cd`, `../../devops/production-readiness`, `../../devops/rollback-planning`, `../../devops/monitoring-logging`, `../../backend/backend-observability`.

## Related Knowledge

`../../../knowledge/` (critical alive-signals per app).

## Related References

`../../../references/testing/` (smoke patterns, when populated).

## Context Loading Guidance

- **Requires:** critical alive-signals, deploy pipeline + health endpoints.
- **Does not require:** full test suites, deep flow detail.
- **May load:** `../../devops/rollback-planning`, `maestro-e2e`.
- **Stop when:** post-build + post-deploy smoke gates are wired.

## Token Efficiency Guidance

The alive-signal list per app is the whole design; keep it short — growth is the anti-pattern.
