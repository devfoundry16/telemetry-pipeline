# ADR-0154: Size batches by payload bytes rather than event count (revision 7)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `ingest-gateway`

## Context

Fixed event counts produced wildly varying payload sizes and unpredictable latency.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Bound batches by serialised byte size.

This applies to `ingest-gateway` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Latency becomes predictable; event-count based dashboards need reinterpretation.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
