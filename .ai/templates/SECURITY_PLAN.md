# Security Plan — <project/feature>

> Fill-in template. Baseline hardening + threat-driven controls, routed to the specialist skills. This plans controls in; assessment of the built system is `../skills/security-review` and the `../skills/security/` pack.

- **Date:** <YYYY-MM-DD> · **Status:** draft | reviewed

## Sensitive Data Classes

| Data class | Sensitivity | Storage / encryption | Retention / deletion |
|------------|-------------|----------------------|----------------------|
| credentials / PII / payment / … | <> | <hash/encrypt/plain> | <> |

## Threats & Mitigations (from threat modeling)

| Trust boundary / asset | Threat (STRIDE) | Mitigation | Owning skill | Testable? |
|------------------------|-----------------|------------|--------------|-----------|
| <> | <> | <> | authentication/authorization/api/…-security | [ ] |

## Controls Checklist

- [ ] Secrets in a store, per-environment, rotatable; none in code/logs/bundles.
- [ ] Input validation server-side at every entry point.
- [ ] AuthN sound; **authZ** enforces ownership/tenant (anti-IDOR) with negative tests.
- [ ] Abuse prevention on public/expensive endpoints (distinct from authZ).
- [ ] Headers/CORS/CSRF/transport correct; dependencies + secrets scanned in CI.
- [ ] Every finding → fix → security regression test.

## Related

- Skills: `../skills/security/README.md`. Never claims "secure"; findings are Confirmed vs Potential.
