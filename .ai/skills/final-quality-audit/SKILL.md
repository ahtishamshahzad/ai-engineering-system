---
name: final-quality-audit
description: Use as the last gate before release to confirm the work is genuinely complete, correct, tested, secure, documented, and in scope. Aggregates the specialist reviews into one go/no-go verdict; blocks on any failed gate.
---

# Final Quality Audit

## Purpose

Provide the final go/no-go before release by confirming all gates are genuinely satisfied — correctness, tests, security, docs, scope — and aggregating specialist reviews into one verdict. Supports Gates 6–7.

## When to Use

- Immediately before release/merge of a completed work item.
- As the closing check of the orchestration pipeline.
- **Not** as a replacement for the specialist reviews it aggregates.

## Inputs

- The completed change set and its work item/acceptance criteria.
- Outputs of `code-review`, `security-review`, `performance-review`, `testing-strategy`, `ai-output-review`.

## Discovery Questions

- Do results meet the original acceptance criteria and success criteria?
- Are Gates 5–6 genuinely passed (not assumed)?
- Is anything out of scope or incomplete?
- Are unrun checks explicitly flagged?

## Responsibilities

- Confirm the work **meets acceptance/success criteria**.
- Confirm **tests** exist and pass (or are flagged) — Gate 5.
- Confirm **security review** done, no open Critical/High — Gate 6.
- Confirm **docs/changelog** updated.
- Confirm **scope** wasn't expanded and nothing is half-done.
- Aggregate specialist findings into **one verdict**; block on any failure.

## Required Workflow

1. Re-read acceptance/success criteria.
2. Collect specialist review outputs.
3. Verify each gate's evidence (not claims).
4. Check scope and completeness.
5. Issue a go/no-go verdict with rationale; list blockers if no-go.

## Decision Rules

- Any open Critical/High security issue → no-go.
- Missing required tests or failing tests → no-go.
- Unmet acceptance criteria or half-done work → no-go.
- Scope expansion beyond approval → flag and split, don't wave through.
- Evidence over claims: "done" requires verification.

## Rules

- Aggregate, don't duplicate the specialists' work — rely on their reports.
- Be honest: distinguish verified from assumed.
- Block on failed gates; never override a gate to ship.

## Anti-Patterns

- Rubber-stamping without checking gate evidence.
- Passing work with open Critical/High issues.
- Accepting "should work" as done.
- Letting scope creep through unremarked.

## Validation Checklist

- [ ] Acceptance/success criteria met.
- [ ] Tests present/passing (or flagged) — Gate 5.
- [ ] Security review done; no open Critical/High — Gate 6.
- [ ] Docs/changelog updated.
- [ ] Scope respected; nothing half-done.
- [ ] Single go/no-go verdict with rationale.

## Definition of Done

A final go/no-go verdict backed by verified gate evidence and aggregated specialist findings; on no-go, a clear, prioritized blocker list.

## Related Skills

`code-review`, `security-review`, `performance-review`, `testing-strategy`, `ai-output-review`, `documentation`, `release-planning`, `project-orchestrator`.

## Related Knowledge

`../../knowledge/`, `../../projects/current/` (gate status).

## Related References

`../../checklists/` (gate checklists).

## Context Loading Guidance

- **Requires:** acceptance criteria, gate status, specialist review outputs.
- **Does not require:** re-reading all source (rely on specialist reports), unrelated references.
- **May load:** any specialist report needed to confirm a gate.
- **Stop when:** the go/no-go verdict is issued.

## Token Efficiency Guidance

Consume specialist summaries rather than re-deriving them. Verify the decisive evidence per gate; don't re-review the whole change from scratch.
