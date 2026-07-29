# ADR 0003 — Idempotency for Financial Writes

## Status
Accepted

## Context

The system must handle retries caused by:

- network failures
- offline synchronization
- duplicated requests

Without protection, financial operations may be executed multiple times.

## Decision

All mutating financial endpoints must implement idempotency using:

- Idempotency-Key (HTTP header)
- request hashing
- database-level constraints
- idempotency_keys table

## Consequences

### Positive

- Prevents duplicate financial operations
- Supports offline-first mobile design
- Enables safe retries

### Negative

- Increased complexity in backend implementation
- Additional storage for idempotency keys

## Alternatives Considered

### No idempotency

Rejected due to unacceptable risk in financial systems.

### Client-side deduplication only

Rejected because backend must guarantee correctness.
