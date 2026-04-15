# ADR-002: Monorepo Structure

- Status: Accepted
- Date: 2026-04-15

## Context

Two deployable apps (NestJS API, Next.js web) plus shared code
(Prisma client, Zod schemas, types). A monorepo avoids version drift
between API contracts and web consumers.

## Decision