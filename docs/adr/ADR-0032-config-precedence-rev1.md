# ADR-0032: Fix a single precedence order for configuration sources (revision 1)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `ingest-gateway`

## Context

Flags, environment, and file config disagreed and the winner varied by code path.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Resolve once at startup with flags over environment over file, and log the result.

This applies to `ingest-gateway` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Behaviour becomes predictable; some existing overrides change meaning.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
