---
name: performance-review
description: Use to find and plan fixes for real performance bottlenecks — measured, not guessed. Covers rendering/re-renders, data access, network, memory, and startup. Recommends safe, measurable improvements over risky rewrites and validates in a representative build.
---

# Performance Review

## Purpose

Identify the **real** bottleneck for a performance concern, trace it to specific code, and plan safe, measurable fixes — validated in a representative build, not judged from a dev/debug environment.

## When to Use

- A change or screen/endpoint is slow, janky, or memory-heavy.
- Before a refactor motivated by performance (`refactor-planning`).
- **Not** as blanket optimization without a measured bottleneck.

## Inputs

- The slow scenario (what, where, which platform, which build).
- Profiling data if available; otherwise a plan to obtain it.

## Discovery Questions

- What is slow, and under what action/scale?
- Which platform/environment, and debug vs release/production?
- Is there a measurement, or must one be taken first?
- Low-end vs high-end conditions?

## Responsibilities

- **Locate the bottleneck** via measurement (profiler, timing, metrics).
- Categorize: rendering/re-renders · data access/queries · network · memory · startup · concurrency.
- Plan **safe, targeted fixes** that preserve behavior.
- **Validate** improvements in a representative build; capture before/after.

## Required Workflow

1. Reproduce and measure the scenario (representative build).
2. Trace the bottleneck to specific code.
3. Plan minimal, behavior-preserving fixes.
4. Apply/plan fixes; re-measure.
5. Record before/after and residual risks.

## Decision Rules

- No optimization without a named, measured bottleneck.
- Prefer small, measurable wins over risky rewrites.
- Don't add heavy dependencies without a measured justification and trade-off note.
- Judge results from release/production-like conditions, not debug.

## Rules

- Preserve behavior; performance work is not a feature change.
- Measure before and after; quote numbers.
- Mark any unrun measurement "unverified until run."

## Anti-Patterns

- Blind micro-optimization / premature memoization.
- Judging performance from a debug build.
- Rewriting instead of fixing the measured hotspot.
- Adding libraries to "make it fast" without data.

## Validation Checklist

- [ ] Scenario reproduced and measured.
- [ ] Bottleneck traced to specific code.
- [ ] Fixes minimal and behavior-preserving.
- [ ] Re-measured in a representative build.
- [ ] Before/after captured; residual risks noted.

## Definition of Done

A performance report identifying the measured bottleneck, a safe fix plan (or applied fixes), before/after numbers from a representative build, and any remaining risks.

## Related Skills

`code-review`, `refactor-planning`, `testing-strategy`, `existing-project-audit`, `final-quality-audit`, `project-orchestrator`.

## Related Knowledge

`../../knowledge/` (data model, hot paths).

## Related References

`../../references/<perf-topic>/` for profiling/tooling if needed.

## Context Loading Guidance

- **Requires:** the slow scenario, measurement or a plan to measure, the hot path code.
- **Does not require:** unrelated modules, the full reference tree, planning skills' bodies.
- **May load:** `refactor-planning` if the fix is structural.
- **Stop when:** before/after is recorded (or measurement is flagged unrun).

## Token Efficiency Guidance

Read only the measured hot path. Quote the decisive metrics; don't paste full profiler dumps.
