---
name: threat-modeling
description: Use to identify what could go wrong before building — assets, trust boundaries, attackers, and threats (STRIDE-style) across the system — producing prioritized, testable mitigations that drive the specialist security skills. Structured thinking, not a scan.
---

# Threat Modeling

## Purpose

Find the threats worth defending against **before** they're built in: map assets, trust boundaries, and attackers, enumerate what could go wrong, and turn that into prioritized mitigations that route to the specialist security skills. It frames the whole security effort; it doesn't scan code.

## When to Use

- At design time for a new system/feature, and when architecture changes trust boundaries.
- **Not** as a code scan (specialist skills) or a post-hoc audit alone (`../../security-review`) — though it guides both.

## Inputs

- Architecture + data flow (`../../architecture-design`), data-sensitivity map (`../../backend/backend-security`).
- Applications, integrations, and who the users/attackers are.

## Discovery Questions

- What are the **assets** worth protecting (user data, money, credentials, availability, reputation)?
- Where are the **trust boundaries** (client↔server, service↔service, tenant↔tenant, third-party edges)?
- Who are the **attackers** (external anonymous, authenticated user abusing scope, malicious insider, compromised dependency)?
- What's the **impact** if each asset is compromised?

## Responsibilities

- Map **assets, entry points, trust boundaries, and data flows** across the system (each boundary is where threats concentrate).
- Enumerate threats systematically (STRIDE as a checklist: **S**poofing, **T**ampering, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**levation of privilege) per boundary/asset.
- **Prioritize by likelihood × impact** — model the plausible, not every theoretical attack.
- Turn each significant threat into a **mitigation routed to a specialist skill**:
  - identity/session threats → `authentication-security`;
  - access/scope/IDOR → `authorization-security`;
  - injection/API abuse → `api-security`, `web-security`;
  - data exposure → `database-security`, `privacy-review`;
  - secret exposure → `secrets-audit`; dependency compromise → `dependency-security`;
  - automated abuse/DoS → `abuse-prevention`; platform-specific → `mobile-security`/`web-security`.
- Make mitigations **testable** — each should become a security regression test (`security-regression-testing`).

## Required Workflow

1. Inventory assets, entry points, trust boundaries, data flows.
2. Enumerate threats per boundary (STRIDE checklist).
3. Prioritize by likelihood × impact.
4. Assign each significant threat a mitigation + owning specialist skill.
5. Record the model; feed mitigations to the specialists and to `security-regression-testing`.

## Decision Rules

- Model the plausible attacker and realistic impact — infinite theoretical threats aren't a plan; prioritize.
- Trust boundaries are where to look hardest — data crossing one is untrusted until validated/authorized.
- Every mitigation names an owner (a specialist skill) and is testable — otherwise it's a wish.
- Revisit the model when architecture changes boundaries; it's living, not one-time.

## Rules

- Threats prioritized, not exhaustively listed without triage.
- Mitigations routed to specialist skills and made testable.
- The model is recorded and revisited on boundary changes.

## Anti-Patterns

- Skipping threat modeling and discovering the attack surface in production.
- An unprioritized wall of every conceivable threat, actioned on none.
- Mitigations with no owner or no test.
- Modeling once and never updating as the system changes.
- Treating a vulnerability scanner's output as a threat model.

## Validation Checklist

- [ ] Assets, entry points, trust boundaries, data flows mapped.
- [ ] Threats enumerated per boundary (STRIDE).
- [ ] Prioritized by likelihood × impact.
- [ ] Each significant threat → mitigation + owning specialist skill.
- [ ] Mitigations testable; fed to `security-regression-testing`.

## Definition of Done

A recorded, prioritized threat model — assets, boundaries, attackers, and STRIDE threats — with each significant threat mapped to a testable mitigation owned by a specialist security skill, revisited when boundaries change.

## Related Skills

`authentication-security`, `authorization-security`, `api-security`, `web-security`, `mobile-security`, `database-security`, `secrets-audit`, `dependency-security`, `abuse-prevention`, `privacy-review`, `security-regression-testing`, `../../security-review`, `../../architecture-design`.

## Related Knowledge

`../../../knowledge/` (assets, attackers, impact tolerances).

## Related References

`../../../references/security/` (threat-model templates, when populated).

## Context Loading Guidance

- **Requires:** architecture + data flow, data-sensitivity map, user/attacker context.
- **Does not require:** code-level detail (specialists handle it), full source.
- **May load:** the specialist skills a threat routes to.
- **Stop when:** the prioritized model + routed mitigations are recorded.

## Token Efficiency Guidance

The threat table (boundary/asset → STRIDE threat → priority → mitigation → owning skill) is the whole artifact.
