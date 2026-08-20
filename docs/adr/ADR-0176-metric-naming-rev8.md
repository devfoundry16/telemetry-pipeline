# ADR-0176: Adopt a single metric naming convention (revision 8)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `rollup-writer`

## Context

Inconsistent names made cross-service dashboards unreliable.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Require a consistent unit-suffixed naming scheme, validated at registration.

This applies to `rollup-writer` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Dashboards compose cleanly; existing names need migration aliases.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
