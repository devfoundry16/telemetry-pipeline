# ADR-0110: Cap label cardinality per metric (revision 5)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `rollup-writer`

## Context

An unbounded label exploded series count and degraded query latency for everyone.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Cap distinct label values per metric and aggregate the overflow into a bucket.

This applies to `rollup-writer` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Query latency stays stable; overflowed detail is unrecoverable.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
