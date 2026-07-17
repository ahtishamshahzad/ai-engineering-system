# Prompt: Performance Audit

> Tool-neutral starter. Measure first; fix the real bottleneck.

---

Do a **performance audit** (`.ai/` canonical; `.ai/workflows/performance-audit.md`, `.ai/skills/performance-review`).

**Symptom:** <endpoint/query/screen, percentile, since when> · **Target:** <the requirement it misses>

Do this:
1. **Measure first** — reproduce and quantify against the target; break down where time goes (query / N+1 / external call / serialization / event-loop). Use `.ai/skills/backend/backend-performance`, `.ai/skills/database/database-performance`, `indexing`, `.ai/skills/mobile/mobile-performance` as the layer demands.
2. Fix the **dominant contributor** with the narrowest safe change — no speculative optimization, no correctness/authorization sacrificed for speed.
3. Re-measure under representative load; record before/after; add a regression guard.

If there is no measurement pointing at a bottleneck, do not optimize — measure first. Report symptom → cause → fix → numbers.
