# Prompt: Bug Investigation

> Tool-neutral starter. Investigate before fixing.

---

Investigate a **bug** (`.ai/` canonical; `.ai/workflows/bugfix.md`, `.ai/skills/bug-investigation`).

**Bug:** <symptom, observed vs expected, where seen>

Do this — **do not fix yet**:
1. Reproduce it (or produce a concrete reproduction plan). If it can't be reproduced, investigate until it can.
2. Find the **root cause**, not the symptom (cite file:line).
3. Scope the **minimal** fix; note the lowest test level that reproduces it.
4. Fill `.ai/templates/BUG.md` (reproduction, root cause, planned regression test).

Run `.ai/hooks/before-bugfix.md`. Report the root cause and the fix plan before implementing.
