# Prompt: Final Audit

> Tool-neutral starter. Aggregate reviews into one go/no-go.

---

Run the **final quality audit** before release (`.ai/` canonical; `.ai/skills/final-quality-audit`).

Do this:
1. Aggregate the outputs of code review, security review, performance, testing/coverage-by-risk, AI-output review, and production-readiness → `.ai/templates/FINAL_AUDIT.md`. Confirm each is done and its critical/high issues are resolved or accepted with rationale — **do not re-run them; verify their evidence.**
2. Confirm delivered scope matches the approved requirements and success criteria (no silent scope change — `.ai/skills/ai-output-review`).
3. Review open risks (`.ai/templates/RISK_REGISTER.md`).
4. Produce a single **GO / NO-GO** with blockers (each owned) if NO-GO.

This feeds the release checklist (Gate 7). Do not ship without explicit approval.
