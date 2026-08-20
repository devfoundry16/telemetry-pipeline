# ADR-0145: Shed load at the gateway rather than queueing without limit (revision 7)

- **Status:** Accepted
- **Date:** 2026-08-20
- **Component:** `ingest-gateway`

## Context

Unbounded queueing turned a throughput problem into an out-of-memory crash.

The cost of leaving this undecided was paid repeatedly: each team hit the
same question and answered it locally, so behaviour varied by code path and
the failure mode only showed up under load.

## Decision

Apply a bounded queue and reject over the limit with a retryable status.

This applies to `ingest-gateway` and to anything that calls it. Existing
call sites are migrated as they are touched rather than in one sweep.

## Consequences

Failures become explicit and retryable; peak burst capacity drops.

The trade is deliberate: predictable behaviour under load is worth more
here than best-case throughput, because the failure it prevents is the one
that pages someone.

## Alternatives considered

- **Do nothing.** Cheapest today, but the failure mode recurs and each
  recurrence costs more than this change.
- **Push the decision to callers.** Flexible, but it reproduces the
  inconsistency this record exists to remove.
