# ADR-0043: Require backward compatibility for minor schema versions (revision 2)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `schema-registry`

## Context

Producers upgraded ahead of consumers and broke normalisation mid-flight.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Reject minor versions that are not backward compatible at registration time.

This applies to `schema-registry` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Rollouts decouple from consumer upgrades; breaking changes need a major version.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
