# ADR-0168: Enforce per-tenant quotas at admission, not at write (revision 8)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `ingest-gateway`

## Context

Over-quota tenants consumed pipeline capacity before being rejected downstream.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Check quota during admission and reject before the batch enters the bus.

This applies to `ingest-gateway` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Wasted work drops sharply; quota state must now be read on the hot path.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
