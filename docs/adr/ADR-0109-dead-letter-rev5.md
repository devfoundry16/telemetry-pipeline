# ADR-0109: Route unparseable events to a dead-letter topic (revision 5)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `event-bus`

## Context

Malformed events blocked the partition and stalled every consumer behind them.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Move events that fail parsing to a dead-letter topic and continue.

This applies to `event-bus` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Head-of-line blocking is gone; silent data loss is possible if the topic is unmonitored.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
