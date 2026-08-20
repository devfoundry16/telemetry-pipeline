# ADR-0139: Sample debug telemetry rather than dropping it wholesale (revision 6)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `ingest-gateway`

## Context

Debug volume was either overwhelming or entirely absent when needed.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Apply deterministic per-trace sampling with a configurable rate.

This applies to `ingest-gateway` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Debug signal survives at bounded cost; rare events may be missed.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
