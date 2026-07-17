# Incident Report — <short title>

> Fill-in template. Timeline, impact, mitigation, root cause, and a **blameless** postmortem that produces a regression test and tracked actions (`../skills/devops/incident-readiness`, `../workflows/incident-response.md`).

- **Incident ID:** <> · **Date:** <YYYY-MM-DD> · **Severity:** SEV1 | SEV2 | SEV3 · **Status:** mitigated | resolved | closed

## Impact

<Who/what was affected, for how long, and how badly.>

## Timeline (UTC)

| Time | Event |
|------|-------|
| <detected> | <> |
| <mitigated> | <rollback? > |
| <resolved> | <> |

## Mitigation

<What restored service (often a rollback). Immediate action taken.>

## Root Cause

<The actual cause — blameless, factual. Not "who," but "what and why.">

## Prevention

- **Regression test added:** <level; guards recurrence — `../skills/testing/regression-testing`>
- **Action items (owned, tracked):**

| Action | Owner | Due | Status |
|--------|-------|-----|--------|
| <> | <> | <> | <> |

## Related

- Workflow: `../workflows/incident-response.md`. Durable fix via `../workflows/bugfix.md`.
