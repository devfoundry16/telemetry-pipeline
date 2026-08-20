# ADR-0037: Derive stage timeouts from an end-to-end budget (revision 1)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `normaliser`

## Context

Per-stage timeouts summed to more than the client deadline, so work continued after the caller gave up.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Pass a deadline through the pipeline and derive each stage timeout from what remains.

This applies to `normaliser` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Wasted post-deadline work disappears; stages must handle an already-expired budget.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
