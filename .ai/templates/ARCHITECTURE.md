# Architecture — <project>

> Fill-in template. Define boundaries, data flow, integrations, and cross-cutting concerns, proportional to the request (`../skills/architecture-design`). Feeds Gate 3 and the file-ownership boundaries used to keep parallel agents disjoint.

- **Date:** <YYYY-MM-DD> · **Status:** draft | approved

## Overview

<One paragraph: the shape of the system and how the pieces relate.>

## Modules / Domains

| Module / domain | Responsibility | Owns (files/dirs) | Depends on |
|-----------------|----------------|-------------------|------------|
| <> | <> | <> | <> |

## Data Flow

<How data moves through the system — request path, async paths. Diagram or bullet list.>

## Integrations

| Integration | Purpose | Direction | Contract / notes |
|-------------|---------|-----------|------------------|
| <> | <> | in/out | <> |

## Cross-Cutting Concerns

- **AuthN / AuthZ:** <approach; server-enforced>
- **Validation:** <where; server authoritative>
- **Error handling:** <central shape>
- **Config / secrets:** <store; per-environment>
- **Observability:** <logging/metrics/health>

## Domain → File Ownership Boundaries

| Agent / area | Owns (disjoint) |
|--------------|-----------------|
| backend | <> |
| database | <schema/migrations — sole owner> |
| web / mobile | <> |

## Related

- From: `STACK_RECOMMENDATION.md`. Next: `REPOSITORY_STRUCTURE.md`, `PHASE_PLAN.md`. Gate 3. Parallel rules: `../agents/multi-agent-execution.md`.
