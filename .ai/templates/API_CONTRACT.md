# API Contract — <api / service>

> Fill-in template. The agreed contract between backend and clients — the single source of truth (`../skills/backend/api-contracts`). Endpoint detail may reference an OpenAPI/GraphQL schema file; this document records conventions and the change policy.

- **Date:** <YYYY-MM-DD> · **Version:** <semver / date> · **Source of truth:** <OpenAPI file | GraphQL SDL | this doc>

## Conventions

- **Style:** REST | GraphQL · **Base path / endpoint:** <>
- **Auth:** <how requests authenticate>
- **Error shape:** <machine code + safe message + request id>
- **Pagination:** <cursor/offset convention>

## Endpoints (or link to schema)

| Method / field | Path / type | Auth required | Request | Response | Notes |
|----------------|-------------|---------------|---------|----------|-------|
| <> | <> | public/user/role/ownership | <> | <> | <> |

## Breaking-Change Policy

- Additive (new optional field, new endpoint) → safe.
- Removals / renames / retypes / newly-required → breaking → **version bump + deprecation window covering the slowest consumer**.

## Consumers

| Consumer | Update cadence (sets deprecation window) |
|----------|------------------------------------------|
| <web/mobile/partner> | <> |

## Related

- Skill: `../skills/backend/api-contracts`. Contract testing: `../skills/testing/contract-testing`.
