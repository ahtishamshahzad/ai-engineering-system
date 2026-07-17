# `.ai/skills/security/` — Security Skill Pack (Index)

Reusable, tool-neutral skills for assessing and hardening a system's security. Discover them **through this index**, then load only the relevant ones (`../../../system/SKILL_SELECTION_RULES.md`). **Do not load the whole pack** — most tasks need 1–3 skills.

These are the **review/audit lens** on security: they find and prioritize risk, separate **Confirmed vs Potential** findings, **never print secrets**, **never declare a system "secure"**, and route every finding to a fix proven by a **security regression test**. The build-side controls live in the backend/database/devops packs — the security pack verifies them. Threat modeling frames the effort; the specialists cover each surface. Aligns with the core `../../security-review`.

## Categories

### Framing
| Skill | Description |
|-------|-------------|
| [`threat-modeling`](threat-modeling/SKILL.md) | Assets, trust boundaries, attackers, STRIDE threats → prioritized, testable, routed mitigations. |

### Identity & Access
| Skill | Description |
|-------|-------------|
| [`authentication-security`](authentication-security/SKILL.md) | Credential storage, session/token handling, revocation, brute-force/enumeration resistance. |
| [`authorization-security`](authorization-security/SKILL.md) | IDOR/BOLA, ownership/tenant scoping, escalation, client-ID trust — proven by negative tests. |

### Surfaces
| Skill | Description |
|-------|-------------|
| [`api-security`](api-security/SKILL.md) | OWASP API risks: validation/injection, object/function authz, mass assignment, resource consumption, leakage. |
| [`web-security`](web-security/SKILL.md) | XSS/CSP, CSRF, cookie flags, headers, clickjacking, open redirects, bundle secrets. |
| [`mobile-security`](mobile-security/SKILL.md) | Secure storage, bundle secrets, transport/pinning, deep-link validation, server-side enforcement. |
| [`database-security`](database-security/SKILL.md) | Least-privilege access, network exposure, injection, encryption, tenant isolation, PII (review lens). |

### Supply Chain & Secrets
| Skill | Description |
|-------|-------------|
| [`secrets-audit`](secrets-audit/SKILL.md) | Find exposed secrets (incl. git history); confirm + drive rotation, not just deletion. |
| [`dependency-security`](dependency-security/SKILL.md) | Vulnerabilities by reachability, supply-chain integrity, malicious packages, CI enforcement. |

### Abuse & Privacy
| Skill | Description |
|-------|-------------|
| [`abuse-prevention`](abuse-prevention/SKILL.md) | Verify public/expensive endpoints are protected; distinct from authorization. |
| [`privacy-review`](privacy-review/SKILL.md) | Data minimization, consent, retention/erasure, PII leak paths, third-party sharing, compliance. |

### Durability
| Skill | Description |
|-------|-------------|
| [`security-regression-testing`](security-regression-testing/SKILL.md) | Findings/threats → CI-gated negative + failing-first tests so regressions can't silently return. |

## Selection guidance (security)

| Situation | Load (only these) |
|---|---|
| Design-time security | `threat-modeling` → the specialist skills its threats route to |
| Auth review | `authentication-security` + `authorization-security` |
| API review | `api-security` (+ `authorization-security` for access depth) |
| Web app review | `web-security` (+ `api-security`) |
| Mobile app review | `mobile-security` |
| Data store review | `database-security` (+ `privacy-review` for PII) |
| Secret/dependency audit | `secrets-audit` + `dependency-security` |
| Public-endpoint abuse | `abuse-prevention` |
| Personal data handling | `privacy-review` |
| Locking in fixes | `security-regression-testing` |

## Dependency guidance (how security skills relate)

- **`threat-modeling` frames** the effort and routes each threat to a specialist; revisit when boundaries change.
- **Specialists own surfaces**: identity (`authentication-security`), access (`authorization-security`), and the API/web/mobile/database surfaces — each is the **review lens** on the corresponding build skill (e.g. `authorization-security` ↔ `../../backend/backend-authorization`; `database-security` ↔ `../../database/database-security`; `abuse-prevention` ↔ `../../backend/rate-limiting`).
- **Cross-cutting**: `secrets-audit` and `dependency-security` cut across all surfaces; `privacy-review` addresses data handling beyond pure security.
- **Everything terminates in tests**: every finding routes to `security-regression-testing`, CI-gated via `../../devops/ci-cd`, and incidents (`../../devops/incident-readiness`) feed new tests.
- **Aggregation**: findings feed the core `../../security-review` and `../../final-quality-audit`; readiness gates at `../../devops/production-readiness`.

## References

`../../../references/security/` — threat-model templates, per-surface review checklists, scan and security-test patterns (populated as the project develops).

## Rule: do NOT load the whole pack

Loading every security skill wastes context (`../../../system/TOKEN_OPTIMIZATION_RULES.md`). Select the minimum relevant set via this index, load one stage at a time, and unload on task switch.
