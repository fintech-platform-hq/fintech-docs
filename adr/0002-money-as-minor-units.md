# ADR 0002 — Store Money as Minor Units

## Status
Accepted

## Context

Financial systems require precision and consistency across:

- backend (Node.js)
- database (PostgreSQL)
- mobile (Swift)

Floating point representations introduce rounding errors.

## Decision

All monetary values will be stored as integer minor units.

Examples:

- 10.50 BRL → 1050
- 25.99 USD → 2599

## Consequences

### Positive

- No floating point errors
- Deterministic calculations
- Consistent behavior across platforms

### Negative

- Requires formatting layer for display
- Requires developers to handle scaling manually

## Alternatives Considered

### DECIMAL / NUMERIC

Rejected due to potential inconsistencies across systems and increased complexity in mobile synchronization.
