# ADR-0004: Local Infrastructure via Docker Compose

- Status: Accepted
- Date: 2026-04-15
- Deciders: @zsyayo112

## Context

开发环境需要 Postgres(带 pgvector,Week 6 RAG 用)和 Redis(Week 5 缓存 +
BullMQ 队列、Week 2 Refresh Token 存储)。团队成员可能用 macOS / Windows /
Linux,本地直装数据库版本不一致、卸载残留、扩展安装繁琐。

## Decision

用 `docker-compose.dev.yml` 统一拉起 Postgres 和 Redis:

- Postgres 镜像选 `pgvector/pgvector:pg16`,免去手动装 vector 扩展
- 通过 `/docker-entrypoint-initdb.d` 初始化脚本启用 `vector` / `pg_trgm` /
  `unaccent` 三个扩展
- Redis 启用 AOF 持久化,限制 256MB 内存 + LRU 淘汰
- 命名 volume(`campsite-os-postgres-data`)避免误删
- 暴露端口可通过 `.env` 覆盖,避免与本机已装的 Postgres/Redis 冲突

## Alternatives Considered

1. **本地直装**:版本漂移、扩展难装、卸载残留,跨平台差异大。否。
2. **官方 `postgres:16` + 启动后手动装 pgvector**:多一步手动,容易忘。否。
3. **生产级编排(K8s/Compose with Traefik)**:开发环境过度。Week 7 部署再考虑。

## Consequences

- ✅ `pnpm db:up` 一键启动,新成员入职 5 分钟跑起来
- ✅ pgvector 开箱即用,Week 6 不用回头改基础设施
- ✅ AOF + 命名 volume,Refresh Token 重启不丢
- ⚠️ 需要本地装 Docker Desktop(macOS/Windows)或 Docker Engine(Linux)
- ⚠️ 端口冲突需手动改 `.env`,新成员第一次跑可能需要排查
