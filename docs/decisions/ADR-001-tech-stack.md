# ADR-001: Tech Stack Selection

- Status: Accepted
- Date: 2026-04-15

## Context

Campsite OS is a ground-up rewrite of the v1 `campsite-manage` system
(Express + CRA + JavaScript). The rewrite serves dual goals: (1) modernize
the architecture for a real camp business, (2) demonstrate full-stack +
AI engineering capability for job applications.

## Decision

| Layer        | Choice                                                       |
|--------------|--------------------------------------------------------------|
| Monorepo     | pnpm workspaces + Turborepo                                  |
| Backend      | NestJS + Prisma + PostgreSQL + Redis + BullMQ + pgvector     |
| Frontend     | Next.js 14 (App Router) + TanStack Query + shadcn/ui         |
| AI           | Vercel AI SDK + LangChain.js + OpenAI / Claude API           |
| Observability| Sentry + OpenTelemetry + Prometheus + Grafana                |
| Testing      | Jest + Supertest + Playwright                                |
| Deployment   | Docker Compose + Nginx + GitHub Actions + GHCR               |

## Rationale

- **TypeScript everywhere**: type safety across API/web/shared schemas via
  Zod, eliminating a class of v1 runtime bugs.
- **NestJS over Express**: built-in DI, decorator-based DTO validation,
  and Controller/Service/Repository convention reduce boilerplate and
  enforce layering.
- **Next.js App Router**: per-page rendering strategy (SSR for SEO pages,
  ISR for marketing pages, CSR for admin), better Lighthouse scores than
  v1 CRA.
- **pgvector** instead of a separate vector DB: one less moving part for
  RAG; sufficient for the project's data scale.
- **Turborepo** over Nx: smaller config surface, sufficient for a
  two-app monorepo.

## Consequences

- Steeper learning curve in weeks 2–4 (TS + NestJS + App Router are new).
- Single Postgres instance handles both relational and vector workloads;
  re-evaluate at >1M chunks.