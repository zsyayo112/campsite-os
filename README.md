# Campsite OS

> Full-stack AI-powered camp operations platform — a ground-up rewrite
> of [campsite-manage](https://github.com/zsyayo112/campsite-manage)
> serving a real forest camp business.

[![CI](https://github.com/zsyayo112|g/campsite-os/actions/workflows/ci.yml/badge.svg)](https://github.com/zsyayo112|g/campsite-os/actions/workflows/ci.yml)

## Why this exists

v1 (Express + CRA + JS) is in production but accumulated technical debt:
no type safety, no layering, no observability, no AI. v2 is a from-scratch
rewrite to demonstrate modern full-stack + AI engineering on a non-trivial
real business domain (bookings, orders, customers, pickup logistics).

## Tech stack

- **Monorepo**: pnpm + Turborepo
- **Backend**: NestJS + Prisma + PostgreSQL (+ pgvector) + Redis + BullMQ
- **Frontend**: Next.js 14 App Router + TanStack Query + shadcn/ui + Tailwind
- **AI**: Vercel AI SDK + LangChain.js + OpenAI / Claude
- **Observability**: Sentry + OpenTelemetry + Prometheus + Grafana
- **Deployment**: Docker Compose + Nginx + GitHub Actions + GHCR

See [`docs/decisions/ADR-001`](docs/decisions/ADR-001-tech-stack.md) for rationale.

## Architecture

See [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md).

## Repository layout
```
apps/
    api/      NestJS API
    web/      Next.js web app
packages/
    database/       Prisma schema and client
    shared/         Cross-package types and Zod schemas
    eslint-config/  Shared ESLint flat configs
    tsconfig/       Shared tsconfigs
docs/
    decisions/      Architecture Decision Records
    architecture/   Diagrams and overview
    benchmarks/     Perf measurements (populated from week 5)
```
## Local development

```bash
# Prerequisites: Node 20.11+, pnpm 9+
pnpm install
pnpm dev          # runs all apps in parallel
pnpm lint         # runs all packages
pnpm typecheck
pnpm build
```

## Roadmap

| Week | Focus |
|------|-------|
| 1    | Monorepo + CI + lint + ADRs |
| 2–3  | NestJS API: Controller/Service/Repository, JWT, Swagger |
| 4    | Next.js web: SSR/ISR public pages, CSR admin |
| 5    | Redis cache + BullMQ + OTel/Prom/Grafana |
| 6    | AI: NL booking assistant, RAG support, Text-to-SQL |
| 7    | Docker multi-stage + GHCR + VPS deploy |
| 8    | Tests + ADRs + README polish |

## License

UNLICENSED — portfolio project.