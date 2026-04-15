# ADR-003: Drop Shuttle Module and SQL Server Sync

- Status: Accepted
- Date: 2026-04-15

## Context

v1 included two complex subsystems:

1. **Shuttle dispatch**: `Vehicle`, `Driver`, `Coach`, `ShuttleStop`,
   `ShuttleSchedule` — full vehicle/driver/route scheduling.
2. **SQL Server dual-write**: business writes mirrored to a legacy
   on-prem SQL Server for the partner PMS.

Both are real production needs in v1 but deliver low signal for a job
portfolio relative to their build cost.

## Decision

- **Drop the shuttle module entirely.** Pickup location is preserved as
  a `pickupLocationId` foreign key on `Order` plus a static
  `PickupLocation` dictionary table, sufficient for daily aggregation
  reporting. Full dispatch deferred to v2.1.
- **Drop SQL Server dual-write.** Re-evaluate when the v2 platform is
  considered for production replacement of v1.

## Rationale

- Shuttle scheduling is a constraint-satisfaction problem with low
  AI/architecture signal. Eight-week budget is better spent on
  observability and AI features.
- Dual-write needs distributed-tx patterns (outbox, CDC) that are real
  but only meaningful with a real legacy system to integrate against —
  a portfolio reviewer cannot evaluate it.

## Consequences

- Daily pickup-location headcount aggregation still works via
  `Order.pickupLocationId` group-by.
- v1 → v2 production cutover (if ever) requires reintroducing both
  modules.