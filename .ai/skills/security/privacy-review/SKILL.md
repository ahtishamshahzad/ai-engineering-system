---
name: privacy-review
description: Use to review how personal data is handled — data classification and minimization, lawful/consented use, retention and deletion (right to erasure), no PII in logs/analytics/seeds, third-party data sharing, and cross-border/compliance constraints. Privacy risk, distinct from pure security.
---

# Privacy Review

## Purpose

Assess whether **personal data** is collected, used, stored, shared, and deleted responsibly and lawfully — minimization, consent, retention/erasure, and leak paths (logs, analytics, third parties). Privacy overlaps security but is distinct: data can be secure yet still mishandled.

## When to Use

- When the system handles personal data, in `../../security-review` or before launch.
- **Not** for pure access-control/injection security (other security skills) — though they intersect at data exposure.

## Inputs

- The data-sensitivity map + data flows (`../../backend/backend-security`, `threat-modeling`).
- Third-party integrations receiving data (`../../backend/third-party-integrations`), applicable regulations.

## Discovery Questions

- What personal data is collected, and is each field actually **needed** (minimization)?
- Is collection/use lawful and consented where required, and is the purpose limited?
- What are the **retention** periods, and does **deletion (erasure)** actually remove data — including backups and third parties?
- Where might PII **leak**: logs, analytics, error reports, dev seeds, exports, third-party sharing?
- Any **cross-border transfer** or regulatory regime (GDPR/CCPA/sector-specific) constraints?

## Responsibilities

- **Classify + minimize**: inventory personal-data fields; flag data collected without a clear need — the least-risky data is what you never collect.
- **Lawful basis + consent**: verify collection/use has a basis and required consent; purpose limitation (data used only for what it was collected for) — especially for analytics/marketing use of operational data.
- **Retention + erasure**: retention periods defined and enforced; **account/data deletion genuinely deletes or anonymizes** (not a soft-delete flag), and the backup-retention interaction is a recorded, compliant policy (`../../database/backup-recovery`, `../../database/database-security`).
- **Leak paths**: no PII in logs/metrics/error reports (`../../devops/monitoring-logging` redaction), analytics events, or dev/test seeds (`../../testing/test-data-management`); exports scoped and access-controlled.
- **Third-party sharing**: what personal data flows to which processors, under what agreement, and is it disclosed (`../../backend/third-party-integrations`)?
- **Cross-border + compliance**: transfers and regime-specific obligations (subject access, erasure, breach notification) surfaced at planning, not discovered at audit.
- Route findings to fixes + regression tests where testable (`security-regression-testing`).

## Required Workflow

1. Inventory + classify personal-data fields; flag over-collection.
2. Verify lawful basis, consent, and purpose limitation.
3. Verify retention + real deletion/erasure (incl. backups, third parties).
4. Audit leak paths (logs, analytics, errors, seeds, exports).
5. Map third-party sharing + cross-border/compliance obligations.
6. Record findings; route to fixes/tests.

## Decision Rules

- Don't collect what you don't need — minimization is the strongest privacy control.
- Deletion must be real: a soft-delete that retains PII fails erasure obligations.
- Purpose limitation matters: reusing operational PII for analytics/marketing without basis is a finding.
- PII in logs/analytics/seeds is a finding even if the store is "secure" — exposure is exposure.

## Rules

- Never reproduce real personal data in findings — reference field/location only.
- Findings separated Confirmed vs Potential; compliance obligations surfaced explicitly.
- Deletion/erasure paths are verified, not asserted.

## Anti-Patterns

- Collecting PII "in case it's useful."
- Soft-delete presented as erasure.
- PII flowing into analytics/logs/error trackers unredacted.
- Production PII in dev/test seeds.
- Third-party data sharing with no agreement or disclosure.
- Discovering GDPR/CCPA obligations at audit instead of design.

## Validation Checklist

- [ ] Personal-data fields classified; over-collection flagged.
- [ ] Lawful basis, consent, purpose limitation verified.
- [ ] Retention + real erasure (incl. backups/third parties) verified.
- [ ] Leak paths (logs/analytics/errors/seeds/exports) clean.
- [ ] Third-party sharing + cross-border/compliance mapped.
- [ ] Findings → fixes/tests.

## Definition of Done

A recorded privacy assessment — data minimization, lawful/consented use, retention and real erasure, leak-path audit, third-party sharing, and compliance obligations — with Confirmed/Potential findings routed to fixes, deletion paths verified, and no personal data reproduced in the report.

## Related Skills

`../../backend/backend-security`, `database-security`, `../../database/database-security`, `../../database/backup-recovery`, `../../devops/monitoring-logging`, `../../testing/test-data-management`, `../../backend/third-party-integrations`, `security-regression-testing`, `../../security-review`, `threat-modeling`.

## Related Knowledge

`../../../knowledge/` (data inventory, regulatory regime, processors).

## Related References

`../../../references/security/` (privacy checklists, when populated).

## Context Loading Guidance

- **Requires:** data-sensitivity map + flows, third-party sharing, regulations.
- **Does not require:** injection/access-control depth (other skills), real PII values.
- **May load:** `database-security`, `../../backend/third-party-integrations`.
- **Stop when:** classification, retention/erasure, leak paths, and compliance are assessed.

## Token Efficiency Guidance

The data-field table (field, needed?, basis, retention, erasure, shared-with) is the artifact; never reproduce actual personal data.
