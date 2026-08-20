# ADR-0051: Drain in-flight windows before shutdown (revision 2)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `rollup-writer`

## Context

Rolling deploys dropped partially aggregated windows and left gaps in rollups.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Stop accepting new work, flush open windows, then exit.

This applies to `rollup-writer` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Deploys stop creating gaps; shutdown takes longer and needs a larger grace period.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
