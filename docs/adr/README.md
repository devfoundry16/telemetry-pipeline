# Architecture Decision Records

An ADR records a decision that was expensive to reach and would be expensive to
re-litigate: why an approach was chosen, what was given up, and what now
constrains us.

## Process

1. Open a pull request adding `ADR-NNNN-<slug>.md`, using the template below.
2. Discuss on the PR. Changes to the decision happen there, not after merge.
3. Merge once accepted. Merged ADRs are immutable — supersede rather than edit.

## Status values

| Status | Meaning |
|---|---|
| Proposed | Under discussion on an open PR |
| Accepted | Merged and in force |
| Superseded | Replaced by a later ADR, which is linked from this one |

## Template

```markdown
# ADR-NNNN: <title>

- **Status:** Proposed
- **Date:** YYYY-MM-DD
- **Component:** <component>

## Context

What forced the decision. Constraints, measurements, failure modes observed.

## Decision

What we are doing, stated so someone can act on it.

## Consequences

What this buys, what it costs, and what it now constrains.

## Alternatives considered

What else was on the table and why it lost.
```

## Numbering

ADR numbers are sequential and never reused, including for rejected proposals.
