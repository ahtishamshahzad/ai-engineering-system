---
name: ai-output-review
description: Use to critically review AI-generated output (plans, code, docs, decisions) for the failure modes AI agents are prone to — unsupported assumptions, invented facts, conflicting architecture, unnecessary dependencies, security risks, missing tests, over-engineering, scope expansion, outdated patterns, incomplete validation, and false claims of completion.
---

# AI Output Review

## Purpose

Act as a skeptical reviewer of AI-generated output before it is trusted or shipped. Catch the specific failure modes AI agents produce, and separate what is verified from what is asserted. Complements `code-review`, `security-review`, and `final-quality-audit`.

## When to Use

- After any AI agent produces a plan, code change, document, or decision.
- Before acting on AI output that affects architecture, security, or scope.
- As an independent-review step in multi-agent runs (`../../system/MULTI_AGENT_RULES.md`).

## Inputs

- The AI-generated output under review.
- The original request, requirements, and approved plan/architecture.

## Discovery Questions

- What was actually requested vs what the output delivered?
- Which claims are backed by evidence vs asserted?
- Does the output contradict the approved architecture or prior decisions?

## Responsibilities

Evaluate the output for each failure mode:
1. **Unsupported assumptions** — claims presented as fact without basis.
2. **Invented facts** — fabricated APIs, files, paths, results, or citations.
3. **Conflicting architecture** — contradicts the approved design or itself.
4. **Unnecessary dependencies** — libraries added without a justified need.
5. **Security risks** — introduced or ignored (hand depth to `security-review`).
6. **Missing tests** — changes without corresponding verification.
7. **Over-engineering** — complexity beyond the requirement.
8. **Scope expansion** — work beyond what was approved.
9. **Outdated patterns** — deprecated/legacy approaches where current ones exist.
10. **Incomplete validation** — checks claimed but not actually run.
11. **False claims of completion** — "done"/"working" without evidence.

Report each hit with location, why it's a problem, and the correction.

## Required Workflow

1. Read the request/plan and the AI output side by side.
2. Walk the 11 failure modes.
3. For each finding: cite location, classify the failure mode, state the fix.
4. Separate **verified** from **asserted** claims.
5. Issue a verdict: accept / revise / reject, with blockers.

## Decision Rules

- Treat any "done/working/verified" without evidence as a **false-completion** finding until proven.
- Fabricated file/API/result → reject that portion outright.
- Architecture conflict or scope expansion → block until reconciled with the approved plan.
- Route deep security/perf concerns to `security-review` / `performance-review`.

## Rules

- Be skeptical by default; assertion is not evidence.
- Cite specifics; no vague "looks off."
- Don't approve output you can't verify — mark it "unverified."

## Anti-Patterns

- Trusting confident phrasing as correctness.
- Passing output with fabricated facts or unrun "validation."
- Missing silent scope expansion because the result "looks good."
- Reviewing only style while ignoring invented facts.

## Validation Checklist

- [ ] All 11 failure modes evaluated.
- [ ] Findings cite location + failure mode + fix.
- [ ] Verified vs asserted separated.
- [ ] Dependency additions justified or flagged.
- [ ] Completion claims backed by evidence or flagged false.
- [ ] Verdict (accept/revise/reject) with blockers.

## Definition of Done

A review report covering all 11 failure modes with located findings and fixes, a clean separation of verified vs asserted, and an accept/revise/reject verdict — no fabricated or unvalidated output passed as done.

## Related Skills

`code-review`, `security-review`, `performance-review`, `final-quality-audit`, `project-orchestrator`, `documentation`.

## Related Knowledge

`../../projects/current/` (approved plan/architecture to check against).

## Related References

`../../checklists/` (review checklists).

## Context Loading Guidance

- **Requires:** the AI output, the original request, and the approved plan/architecture.
- **Does not require:** the whole repo (unless verifying a specific claim), unrelated references.
- **May load:** `security-review`/`performance-review` for deep concerns; the specific file to verify an invented-fact claim.
- **Stop when:** the verdict and findings are delivered.

## Token Efficiency Guidance

Compare output against the recorded plan, not the entire history. Verify suspicious claims by checking the specific file/command rather than reloading everything.
