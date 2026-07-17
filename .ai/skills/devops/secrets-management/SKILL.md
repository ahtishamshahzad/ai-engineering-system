---
name: secrets-management
description: Use to plan secret storage and handling — a secret store (not git), per-environment separation, least-privilege access, rotation, injection at runtime/CI, and keeping secrets out of code, logs, images, and client bundles. Auditing existing secret exposure is secrets-audit.
---

# Secrets Management

## Purpose

Keep credentials, tokens, and keys safe throughout their lifecycle: stored in a real secret store, separated per environment, least-privilege, rotatable, and injected only where needed — never in code, logs, images, or client bundles.

## When to Use

- When establishing how a project handles secrets, or onboarding a new secret/integration.
- **Not** for auditing existing exposure (`secrets-audit`) or non-secret config (`environment-management`).

## Inputs

- The secrets inventory (DB creds, API keys, signing keys, cache tokens) and who/what needs each.
- Environments and the deploy/CI platform (`ci-cd`, `github-actions`).

## Discovery Questions

- Where do secrets live today, and is anything in git history (→ `secrets-audit`)?
- What store fits (platform secret manager, cloud KMS/Secrets Manager, Vault) given the deploy target?
- Which secrets differ per environment, and what's the rotation story for each?

## Responsibilities

- Choose a **secret store** appropriate to the deploy target (platform-native secret manager, cloud Secrets Manager/KMS, or Vault) — secrets live there, **never in git** or plaintext config files.
- **Separate per environment**: distinct secrets for dev/staging/production; production secrets never on developer machines or in lower environments.
- Apply **least privilege**: each service/CI job gets only the secrets it needs, scoped; no shared god-credential.
- **Inject at runtime / CI-time**: containers and processes receive secrets via env/mounted secrets at start (`environment-management`, `docker-foundation`); CI reads them from the platform's secret store (`github-actions`), never echoed into logs.
- Plan **rotation**: how each secret is rotated, how the app picks up new values (and reuse-detection for tokens where relevant — `../../backend/backend-authentication`); compromised secrets have a documented revoke path.
- Keep secrets **out of everywhere they leak**: code, logs (`../../backend/backend-observability` redaction), error messages, Docker layers, and client bundles (`environment-management` public/secret boundary).
- Coordinate remediation of any exposed secret with `secrets-audit` (rotate, don't just delete).

## Required Workflow

1. Inventory secrets + consumers.
2. Choose the store; move secrets out of git/config into it.
3. Separate per environment; scope least-privilege access.
4. Wire runtime/CI injection (no logging).
5. Define rotation + revocation per secret.

## Decision Rules

- The store is the source of truth; git and plaintext files are never it.
- Production secrets stay out of dev/staging and off laptops.
- A committed secret is compromised — rotate it, deletion alone is insufficient (`secrets-audit`).
- Least privilege per consumer; scope beats convenience.

## Rules

- No secrets in code, logs, images, client bundles, or git.
- Per-environment separation is absolute.
- Every secret has an owner and a rotation/revoke path.

## Anti-Patterns

- Secrets in `.env` committed to git, or hardcoded in source.
- One shared credential across all services/environments.
- Secrets echoed into CI logs.
- Production keys on developer machines.
- Baked into Docker images or shipped in client bundles.

## Validation Checklist

- [ ] Secret store chosen; secrets out of git/plaintext.
- [ ] Per-environment separation enforced.
- [ ] Least-privilege scoped access per consumer.
- [ ] Runtime/CI injection with no logging.
- [ ] Rotation + revocation defined per secret.

## Definition of Done

A recorded secrets design — a real store, per-environment separation, least-privilege access, runtime/CI injection without logging, and rotation/revocation paths — with no secret in code, logs, images, bundles, or git.

## Related Skills

`secrets-audit`, `environment-management`, `docker-foundation`, `ci-cd`, `github-actions`, `../../security/secrets-audit`, `../../backend/backend-security`, `../../backend/backend-observability`.

## Related Knowledge

`../../../knowledge/` (secret inventory, deploy platform).

## Related References

`../../../references/devops/` (store setup notes, when populated).

## Context Loading Guidance

- **Requires:** secret inventory + consumers, environments, deploy/CI platform.
- **Does not require:** actual secret values (never handle in the clear), app code.
- **May load:** `secrets-audit`, `environment-management`.
- **Stop when:** store, separation, access, injection, and rotation are recorded.

## Token Efficiency Guidance

The secret × (consumer, environment, store location, rotation) table is the artifact; never print real secret values.
