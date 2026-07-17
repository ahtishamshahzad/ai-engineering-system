# Repository Structure — <project>

> Fill-in template. Define the on-disk layout (single-app vs monorepo, shared code) (`../skills/repository-architecture`, `../skills/devops/repository-strategy`).

- **Date:** <YYYY-MM-DD> · **Status:** draft | approved

## Shape

- **Type:** single-app | monorepo | multi-repo
- **Rationale:** <coupling / shared code / cadence / ownership>
- **Tooling (if monorepo):** <pnpm/npm workspaces + Turborepo? why>

## Layout

```
<root>/
├── apps/            # <deployables>
│   ├── <app-a>/
│   └── <app-b>/
├── packages/        # <shared code>
│   └── <shared>/
└── <config, ci, etc.>
```

## Shared Code

| Package | Contents | Consumed by |
|---------|----------|-------------|
| <> | <types/contracts/utils> | <> |

## Conventions

- <Naming, import boundaries, where cross-app contracts live>

## Related

- From: `ARCHITECTURE.md`. DevOps: `../skills/devops/monorepo-selection`. Reference layout notes: `../references/`.
