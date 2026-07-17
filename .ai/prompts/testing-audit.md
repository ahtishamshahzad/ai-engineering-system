# Prompt: Testing Audit

> Tool-neutral starter. Assess coverage by risk, not percentage.

---

Do a **testing audit** (`.ai/` canonical; `.ai/workflows/testing-audit.md`, `.ai/skills/testing/test-coverage-audit`).

**Scope:** <system / area>

Do this:
1. Build/confirm the critical-path + risk map; check each critical path is tested at the right level (`.ai/skills/testing/testing-selection` pyramid).
2. Check the **required cases** on key behaviors — happy · invalid input · error · **authorization denial** · regression · critical env validation — flag missing ones (especially error/denial branches).
3. Use coverage numbers only as a hint; recommend targeted tests prioritized **by risk**.
4. Flag flaky tests for root-cause fixing (`.ai/skills/testing/flaky-test-audit`).

**Do not chase a coverage percentage without risk justification.** Report gaps and prioritized recommendations.
