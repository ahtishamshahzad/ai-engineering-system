---
name: mobile-security
description: Use to review mobile app security — secure storage of tokens/secrets, no secrets in the bundle, secure transport/pinning where warranted, deep-link and IPC validation, platform permissions, and the rule that the server enforces all authorization. The security lens on the mobile pack.
---

# Mobile Security

## Purpose

Assess a mobile app's security given its reality: the device is untrusted, the bundle is extractable, and client checks are UX. Verify sensitive data is stored/transmitted safely, entry points (deep links, IPC) are validated, and the **server** — not the app — enforces access. The build side lives in the mobile pack.

## When to Use

- Reviewing a React Native/Expo app's security, in `../../security-review`.
- **Not** for building mobile features (`../../mobile/` pack) or backend/API security (`api-security`).

## Inputs

- The mobile app's auth/storage/transport/deep-link implementations (`../../mobile/mobile-authentication`, `mobile-secure-storage`, `mobile-deep-linking`).
- Threat model's client threats (`threat-modeling`).

## Discovery Questions

- Where are tokens/sensitive data stored — Keychain/Keystore (secure) or AsyncStorage/plaintext (not)?
- Are any secrets/API keys **in the app bundle** (extractable) that grant real privilege?
- Is transport TLS-only, and is certificate pinning warranted for the threat model?
- Are deep links, universal links, and IPC inputs validated and authorized before acting?

## Responsibilities

- **Secure storage**: tokens, credentials, and sensitive data in Keychain (iOS) / Keystore (Android) via secure storage (`../../mobile/mobile-secure-storage`) — not AsyncStorage/plaintext; nothing sensitive in logs.
- **No privileged secrets in the bundle**: the app bundle is extractable — any embedded secret is public. Backend/provider secrets stay server-side; only public config ships (`../../mobile/mobile-environment-config`). Flag embedded privileged keys.
- **Transport**: TLS everywhere; assess **certificate pinning** against the threat model (valuable for high-risk apps, with a rotation plan; not free — record the decision).
- **Entry-point validation**: deep/universal links and any IPC are untrusted input — validate and authorize before acting (`../../mobile/mobile-deep-linking`); a link must not perform privileged actions without server-side authZ.
- **Server enforces authorization**: client-side gating/hiding is UX only; verify the server checks every protected action — a jailbroken/modified client bypasses all client checks (`../../mobile/mobile-authorization`, `authorization-security`).
- **Platform hygiene**: least-privilege permissions, no sensitive data in screenshots/backups where avoidable, no debug/logging leaks in release builds.
- Route findings to fixes + regression tests (`security-regression-testing`).

## Required Workflow

1. Review sensitive-data storage (secure enclave vs plaintext).
2. Scan the bundle for embedded privileged secrets.
3. Assess transport + pinning against the threat model.
4. Validate deep-link/IPC entry points + their authorization.
5. Confirm server-side enforcement of all client-gated actions.
6. Check permissions/logging/backup hygiene; record findings + tests.

## Decision Rules

- Anything in the bundle is public — embedded privileged secrets are confirmed findings.
- Client checks are UX; the server is the boundary — an unenforced-server-side action is the real finding, not the missing client gate.
- Secure enclave (Keychain/Keystore) for anything sensitive; AsyncStorage for tokens is a finding.
- Certificate pinning is a threat-model decision with a rotation cost — recommend where warranted, don't mandate blindly.

## Rules

- Never print discovered secrets/tokens — location + severity only (`secrets-audit`).
- Findings separated Confirmed vs Potential; never declare the app "secure" (`../../security-review`).
- Server-side enforcement verified for every client-gated action.

## Anti-Patterns

- Tokens/secrets in AsyncStorage or logs.
- Backend/provider keys embedded in the bundle.
- Trusting client-side authorization because "the app hides it."
- Deep links triggering privileged actions without server authorization.
- Debug logging/verbose errors shipped in release builds.

## Validation Checklist

- [ ] Sensitive data in Keychain/Keystore, not plaintext.
- [ ] No privileged secrets in the bundle.
- [ ] TLS enforced; pinning decision recorded.
- [ ] Deep-link/IPC inputs validated + authorized.
- [ ] Server enforces all client-gated actions.
- [ ] Permissions/logging/backup hygiene; findings → tests.

## Definition of Done

A recorded mobile security assessment — secure storage, bundle secret exposure, transport/pinning, entry-point validation, server-side enforcement, and platform hygiene — with Confirmed/Potential findings routed to fixes and regression tests, and no "secure" claim.

## Related Skills

`../../mobile/mobile-secure-storage`, `../../mobile/mobile-authentication`, `../../mobile/mobile-authorization`, `../../mobile/mobile-deep-linking`, `../../mobile/mobile-environment-config`, `authorization-security`, `secrets-audit`, `security-regression-testing`, `../../security-review`, `threat-modeling`.

## Related Knowledge

`../../../knowledge/` (client threats, data sensitivity).

## Related References

`../../../references/security/` (mobile review checklists, when populated).

## Context Loading Guidance

- **Requires:** the app's storage/auth/transport/deep-link impl, client threats.
- **Does not require:** backend internals beyond the enforcement contract.
- **May load:** `../../mobile/mobile-secure-storage`, `authorization-security`.
- **Stop when:** findings + routed fixes/tests are recorded.

## Token Efficiency Guidance

The finding table (area → issue → severity → fix) is the artifact; the server-enforcement check is the highest-value item.
