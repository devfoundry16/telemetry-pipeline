# ADR-0144: Define a fixed lateness horizon for windowed aggregation (revision 7)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `normaliser`

## Context

Events arriving hours late silently reopened closed windows and rewrote published rollups.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Accept lateness up to a fixed horizon, route the rest to a repair topic.

This applies to `normaliser` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Published rollups stop changing after the horizon; very late data needs a repair pass.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
