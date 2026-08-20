# ADR-0046: Partition by tenant and source rather than by event id (revision 2)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `event-bus`

## Context

Random partitioning spread one tenant's ordering across every consumer.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Key partitions on tenant plus source so related events stay ordered.

This applies to `event-bus` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Per-source ordering holds; a large tenant can now hot-spot a partition.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
