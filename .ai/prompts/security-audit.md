# Prompt: Security Audit

> Tool-neutral starter. Independent security assessment.

---

Do a **security audit** (`.ai/` canonical; `.ai/workflows/security-audit.md`, `.ai/skills/security-review` + `.ai/skills/security/`).

**Scope:** <system / change / surface> · **Threat context:** <who/what to defend against>

Assess the relevant surfaces (threat model, authentication, **authorization/IDOR**, api/web/mobile/database security, secrets, dependencies, abuse prevention, privacy) → `.ai/templates/AUDIT.md` / `SECURITY_PLAN.md`.

Rules:
- Separate **Confirmed** from **Potential** findings; give severity and location.
- **Never print secret values** — reference location only, flag for rotation.
- **Never declare the system "secure."**
- Route each finding to a fix owner and a **security regression test** (`.ai/skills/security/security-regression-testing`).

Report blockers for Gate 6.
