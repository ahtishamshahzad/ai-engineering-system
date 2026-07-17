---
name: environment-management
description: Use to plan environment configuration across dev/staging/production — one immutable artifact configured by environment, validated typed env vars (fail fast at boot), no secrets in client bundles, and parity so behavior differs only by config. Secret storage itself is secrets-management.
---

# Environment Management

## Purpose

Make configuration across environments predictable and safe: the **same artifact** everywhere, differing only by injected, **validated** config — so environments stay in parity and misconfiguration fails loudly at startup, not silently in production.

## When to Use

- When defining how dev/staging/production configuration works for an app or service.
- **Not** for secret storage/rotation mechanics (`secrets-management`) or test envs (`../../testing/test-environment-management`).

## Inputs

- The environments (dev, staging, production, preview) and what legitimately differs.
- The apps and their runtimes (server config vs client-bundle constraints).

## Discovery Questions

- What differs per environment (URLs, feature flags, limits) — and what must **not** (behavior, code)?
- Which config is public (safe in a client bundle) vs secret (server-only)?
- How is required config validated so a missing/malformed value stops boot rather than causing later failures?

## Responsibilities

- Establish **one immutable artifact configured by environment** (`../../backend/backend-deployment`, `docker-foundation`) — no per-environment builds; config injected at runtime.
- Define a **typed, validated config schema**: required vars present and well-formed, checked at **boot (fail fast)** with a clear error naming the missing/invalid var (`../../backend/backend-security`, `../../environment-audit`).
- Enforce the **public/secret boundary**: client bundles carry only public config (anything shipped to the browser/app **is** public); secrets stay server-side (`secrets-management`). No `NEXT_PUBLIC_`/`EXPO_PUBLIC_`-style exposure of secrets.
- Maintain **parity**: staging mirrors production config shape (`staging-environment`); differences are values, not structure — divergence is where "works in staging, breaks in prod" is born.
- Provide a documented config template (e.g. `.env.example`) listing every var with type/purpose; real `.env` files git-ignored.
- Coordinate change: config changes are reviewed and rolled out like code, not hand-edited in a console untracked.

## Required Workflow

1. Enumerate environments + what legitimately differs.
2. Define the typed config schema (required/optional, formats).
3. Add boot-time validation that fails fast with clear messages.
4. Enforce the public/secret split; keep secrets server-side.
5. Ensure parity + a documented config template; git-ignore real values.

## Decision Rules

- Same artifact, config-injected — per-environment builds break parity and reproducibility.
- Validate at boot: a false start with a clear error beats a silent misconfiguration surfacing hours later.
- Anything in a client bundle is public — treat it that way, no exceptions.
- Config structure is identical across environments; only values differ.

## Rules

- Real secret values never committed; template lists vars without values.
- Required config validated before the app serves traffic.
- Config changes tracked and reviewed, not console-hand-edited.

## Anti-Patterns

- Building a separate artifact per environment.
- Secrets shipped in client bundles via public env prefixes.
- Missing env var discovered as a runtime crash mid-request, not at boot.
- Staging config structurally diverging from production.
- Untracked console edits to production config.

## Validation Checklist

- [ ] Environments + legitimate differences enumerated.
- [ ] Typed config schema with boot-time fail-fast validation.
- [ ] Public/secret boundary enforced; no secrets in client bundles.
- [ ] Parity maintained; documented template; real values git-ignored.
- [ ] Config changes tracked/reviewed.

## Definition of Done

One artifact configured per environment with a typed, boot-validated config schema, a strict public/secret boundary, staging↔production parity, and a documented template — misconfiguration fails fast, secrets never reach clients.

## Related Skills

`secrets-management`, `staging-environment`, `../../backend/backend-deployment`, `docker-foundation`, `../../environment-audit`, `../../backend/backend-security`, `../../testing/test-environment-management`.

## Related Knowledge

`../../../knowledge/` (environment topology, config inventory).

## Related References

`../../../references/devops/` (config templates, when populated).

## Context Loading Guidance

- **Requires:** environment list, config inventory, app runtimes.
- **Does not require:** secret values, app feature code.
- **May load:** `secrets-management`, `staging-environment`.
- **Stop when:** schema, validation, boundary, and parity are recorded.

## Token Efficiency Guidance

The config schema table (var, type, required, public/secret, per-env differences) is the artifact.
