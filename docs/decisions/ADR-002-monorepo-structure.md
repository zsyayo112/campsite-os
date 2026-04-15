# ADR-002: Monorepo Structure

- Status: Accepted
- Date: 2026-04-15

## Context

Two deployable apps (NestJS API, Next.js web) plus shared code
(Prisma client, Zod schemas, types). A monorepo avoids version drift
between API contracts and web consumers.

## Decision
```
apps/
    api/      # NestJS
    web/      # Next.js
packages/
    database/       # Prisma schema + generated client + repositories
    shared/         # Zod schemas, DTO types, shared utilities
    eslint-config/  # Flat ESLint configs (base/nest/next)
    tsconfig/       # Base tsconfigs

- `apps/*` are deployable units, never imported by other packages.
- `packages/*` are internal libraries, prefix `@campsite-os/`.
- Web imports types from `@campsite-os/shared` only, never from `apps/api`.
```
## Rationale

- Splitting `database` from `shared` keeps Prisma client out of the
  browser bundle.
- ESLint and tsconfig as packages enforce consistency without copy-paste.

## Consequences

- All cross-package types must be exported via `packages/shared`, not
  inferred from API code.
- Adding a new app means adding it to `pnpm-workspace.yaml` and Turborepo
  picks it up automatically.