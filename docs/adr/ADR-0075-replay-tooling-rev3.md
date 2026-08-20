# ADR-0075: Support bounded replay from the repair topic (revision 3)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `rollup-writer`

## Context

Recovering from a bad deploy meant reprocessing everything or nothing.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Add replay over an explicit time range writing through the idempotent path.

This applies to `rollup-writer` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Targeted recovery becomes routine; replay competes with live traffic for capacity.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
