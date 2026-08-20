# ADR-0087: Trust broker ingest time over agent-reported timestamps (revision 4)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `normaliser`

## Context

Agents with skewed clocks placed events in the wrong aggregation window.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Record both, but window on broker ingest time.

This applies to `normaliser` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Windows stop depending on fleet clock health; agent-side latency is less visible.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
