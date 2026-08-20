# telemetry-pipeline

Streaming telemetry ingest and aggregation service.

Accepts high-volume event batches from fleet agents, normalises them against a
versioned schema, and writes rollups to the metrics store. Designed for
at-least-once delivery with idempotent writes, so a replayed batch is a no-op
rather than a double count.

## Architecture

```
agents ──▶ ingest-gateway ──▶ event-bus ──▶ normaliser ──▶ rollup-writer ──▶ metrics store
                 │                              │
                 └── schema-registry ───────────┘
```

| Component | Responsibility |
|---|---|
| `ingest-gateway` | Auth, quota enforcement, batch validation, backpressure |
| `normaliser` | Schema resolution, unit conversion, late-event handling |
| `rollup-writer` | Windowed aggregation, idempotent upserts |
| `schema-registry` | Versioned event schemas and compatibility checks |

## Repository layout

| Path | Contents |
|---|---|
| `docs/adr/` | Architecture decision records |
| `docs/runbooks/` | On-call procedures |

## Decision records

Design decisions are recorded as ADRs under [`docs/adr/`](docs/adr/). Each ADR
captures the context, the decision taken, and the consequences accepted — so the
reasoning survives past the people who were in the room.

New ADRs are proposed as pull requests and merged once accepted. See
[`docs/adr/README.md`](docs/adr/README.md) for the process.

## Status

Pre-production. Interfaces are not yet stable.
