---
name: staging-environment
description: Use to plan a staging environment that meaningfully mirrors production — same artifact, production-parity config/data-shape, isolated from production data, exercising migrations and integrations before prod, and running smoke/E2E as a pre-production gate.
---

# Staging Environment

## Purpose

Provide a pre-production environment close enough to production that passing there predicts production success — same artifact, parity config, realistic-but-isolated data, real migrations and integrations — so releases are validated before they reach users.

## When to Use

- When establishing the path to production (between CI and prod deploy).
- **Not** for test environments (`../../testing/test-environment-management`) or production itself (`production-readiness`).

## Inputs

- Deployment targets (`deployment-selection`) and environment config model (`environment-management`).
- Migration flow (`../../database/database-migrations`) and integrations (`../../backend/third-party-integrations`).

## Discovery Questions

- What must staging mirror to be predictive (artifact, config shape, data volume/shape, integrations)?
- How is staging isolated from production data and side effects (no real charges/emails to real users)?
- What runs against staging as the pre-prod gate (migrations, smoke, E2E)?

## Responsibilities

- Deploy the **same immutable artifact** that will go to production (`../../backend/backend-deployment`), configured for staging — parity means config values differ, structure doesn't (`environment-management`).
- Keep **data-shape parity** with production (realistic volume so performance/pagination issues surface) while **isolated from production data** — no real customer PII; sandbox/sink modes for integrations so staging never charges cards or emails real users (`../../backend/third-party-integrations`, `../../security/privacy-review`).
- **Exercise migrations on staging first** (production-like dataset) to catch lock/timing/ordering issues before prod (`../../database/database-migrations`, `../../database/data-migration`).
- Run **smoke + critical E2E** against staging as a gate (`../../testing/smoke-testing`, `../../testing/playwright-e2e`/`maestro-e2e`); validate env/config there too.
- Treat staging as **production-like**, not a scratch pad — drift from production makes it non-predictive; keep it in sync.

## Required Workflow

1. Define parity requirements (artifact, config, data shape, integrations).
2. Stand up staging with the same artifact + staging config.
3. Isolate data + integrations (sandbox/sink; no prod PII).
4. Run migrations, smoke, and E2E as the pre-prod gate.
5. Keep staging in sync with production to stay predictive.

## Decision Rules

- Same artifact as production — a differently-built staging validates the wrong thing.
- Config structure identical to prod; only values differ.
- Realistic data volume (perf-predictive) but zero production PII (privacy/security).
- Staging that has drifted from production is worse than none — it gives false confidence.

## Rules

- No production data/PII in staging.
- Integrations in sandbox/sink mode — no real-world side effects.
- Staging validates the exact artifact bound for production.

## Anti-Patterns

- Staging built differently from production.
- Production database dump (real PII) loaded into staging.
- Integrations hitting live third parties (real charges/emails).
- Tiny data volumes hiding performance problems.
- Staging left to rot out of sync with prod.

## Validation Checklist

- [ ] Same artifact as production, staging-configured.
- [ ] Config parity (structure identical, values differ).
- [ ] Data-shape parity, isolated, no prod PII.
- [ ] Integrations sandboxed/sinked.
- [ ] Migrations + smoke + E2E run as the pre-prod gate.
- [ ] Kept in sync with production.

## Definition of Done

A staging environment running the same artifact with production-parity config and isolated realistic data, integrations sandboxed, exercising migrations and smoke/E2E as a predictive pre-production gate — kept in sync with production.

## Related Skills

`deployment-selection`, `environment-management`, `production-readiness`, `../../backend/backend-deployment`, `../../database/database-migrations`, `../../database/data-migration`, `../../testing/smoke-testing`, `../../testing/playwright-e2e`, `../../backend/third-party-integrations`, `../../security/privacy-review`.

## Related Knowledge

`../../../knowledge/` (parity requirements, integration list).

## Related References

`../../../references/devops/` (staging setup notes, when populated).

## Context Loading Guidance

- **Requires:** deployment targets, config model, migration flow, integrations.
- **Does not require:** production credentials/data, app feature code.
- **May load:** `production-readiness`, `../../database/database-migrations`.
- **Stop when:** parity, isolation, and the pre-prod gate are defined.

## Token Efficiency Guidance

The parity checklist (artifact, config, data, integrations) is the artifact; keep it to what makes staging predictive.
