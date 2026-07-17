# `.ai/skills/devops/` — DevOps Skill Pack (Index)

Reusable, tool-neutral skills for repository strategy, build/CI/CD, deployment, and production operations. Discover them **through this index**, then load only the relevant ones (`../../system/SKILL_SELECTION_RULES.md`). **Do not load the whole pack** — most tasks need 1–3 skills.

DevOps skills treat repo shape and deployment targets as **decisions** (not defaults), **generate CI from the selected applications** (never jobs for apps that don't exist), keep one immutable artifact configured per environment, and gate production behind readiness + approval. They **do not scaffold infrastructure**. They coordinate with the backend (deployment), testing (CI jobs, smoke), and security (secrets, dependency, CI security checks) packs.

## Categories

### Repository & Monorepo
| Skill | Description |
|-------|-------------|
| [`repository-strategy`](repository-strategy/SKILL.md) | Single repo vs monorepo vs multi-repo, by coupling/cadence/ownership. |
| [`monorepo-selection`](monorepo-selection/SKILL.md) | Workspace manager (pnpm/npm) and whether to add Turborepo. |
| [`turborepo-foundation`](turborepo-foundation/SKILL.md) | Task pipeline, correct cache inputs/outputs, affected-only CI. |
| [`pnpm-workspaces`](pnpm-workspaces/SKILL.md) | pnpm monorepo: layout, `workspace:` linking, strict isolation, frozen lockfile. |
| [`npm-workspaces`](npm-workspaces/SKILL.md) | npm monorepo: layout, linking, explicit deps, `npm ci` discipline. |

### Build & Environment
| Skill | Description |
|-------|-------------|
| [`docker-foundation`](docker-foundation/SKILL.md) | Multi-stage, pinned small base, cached layers, non-root, no baked secrets. |
| [`environment-management`](environment-management/SKILL.md) | One artifact per environment; validated typed config; no secrets in bundles. |
| [`secrets-management`](secrets-management/SKILL.md) | Secret store, per-env separation, least privilege, rotation, runtime injection. |

### CI/CD
| Skill | Description |
|-------|-------------|
| [`ci-cd`](ci-cd/SKILL.md) | Pipeline generated from selected apps + tests; jobs only for apps that exist; deploys approval-gated. |
| [`github-actions`](github-actions/SKILL.md) | Implement the CI/CD design: pinned actions, least-privilege token, environment-gated deploys. |

### Deployment & Environments
| Skill | Description |
|-------|-------------|
| [`deployment-selection`](deployment-selection/SKILL.md) | Deployment target per app (most-managed sufficient option); approval-gated. |
| [`staging-environment`](staging-environment/SKILL.md) | Production-parity, isolated staging; migrations + smoke/E2E as pre-prod gate. |
| [`production-readiness`](production-readiness/SKILL.md) | Evidence-based go/no-go across tests/security/config/data/observability/rollback/incident. |

### Operations
| Skill | Description |
|-------|-------------|
| [`monitoring-logging`](monitoring-logging/SKILL.md) | Centralized logs, dashboards, uptime checks, owned symptom-based alerts. |
| [`rollback-planning`](rollback-planning/SKILL.md) | Fast artifact redeploy, migration reversibility, feature-flag kill switches, rehearsed. |
| [`incident-readiness`](incident-readiness/SKILL.md) | On-call, severities, runbooks, blameless postmortems feeding regression tests. |

## Selection guidance (devops)

| Situation | Load (only these) |
|---|---|
| Repo layout decision | `repository-strategy` (→ `monorepo-selection` → foundation) |
| Monorepo tooling | `monorepo-selection` → `turborepo-foundation` + `pnpm-workspaces`/`npm-workspaces` |
| Containerizing a service | `docker-foundation` |
| Config & secrets | `environment-management` + `secrets-management` |
| Pipeline | `ci-cd` → `github-actions` |
| Choosing where to deploy | `deployment-selection` |
| Path to production | `staging-environment` → `production-readiness` |
| Running in production | `monitoring-logging` + `rollback-planning` + `incident-readiness` |

## Dependency guidance (how devops skills relate)

- **Repo shape decided first** (`repository-strategy`) → `monorepo-selection` → workspace + optional Turborepo foundations.
- **CI is generated from selected applications + test selection** (`ci-cd`), implemented on the platform (`github-actions`); never invents jobs for absent apps.
- **Build/config/secrets** (`docker-foundation`, `environment-management`, `secrets-management`) feed the pipeline and deployment; one artifact, config-injected.
- **Deployment** chains `deployment-selection` → `staging-environment` → `production-readiness` (the release gate), aligned with `../backend/backend-deployment` and `../release-planning`.
- **Operations** run continuously: `monitoring-logging` → `incident-readiness`, with `rollback-planning` as the deploy-reversal mechanism.
- **Cross-pack**: security checks in CI ↔ `../security/dependency-security`/`secrets-audit`; smoke ↔ `../testing/smoke-testing`; migrations/backups ↔ `../database/`.

## References

`../../references/devops/` — repo/monorepo notes, Dockerfile and workflow patterns, config templates, deployment comparisons, runbooks and readiness checklists (populated as the project develops).

## Rule: do NOT load the whole pack

Loading every devops skill wastes context (`../../system/TOKEN_OPTIMIZATION_RULES.md`). Select the minimum relevant set via this index, load one stage at a time, and unload on task switch.
