# Prompt: Code Review

> Tool-neutral starter. Independent review of a change.

---

Do a **code review** (`.ai/` canonical; `.ai/skills/code-review`, `.ai/skills/ai-output-review`).

**Change under review:** <diff / branch / files>

Review for:
- **Correctness**, clarity, and convention-fit with the surrounding code.
- **Scope discipline** — no scope creep, no false completion, no silent changes (AI-output failure modes).
- Route **security** depth to `.ai/skills/security-review` and **performance** depth to `.ai/skills/performance-review` where relevant.

Report findings **by severity** with file:line and a concrete recommendation. Do not rewrite the code — hand findings to the author. State whether anything blocks Gate 6. Hook: `.ai/hooks/before-pr.md`.
