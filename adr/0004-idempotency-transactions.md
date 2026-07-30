# ADR 0004 — Idempotency for Transactions

## Decision

Transactions endpoint implements idempotency using:

- Idempotency-Key (UUID)
- request_hash comparison
- response persistence
- PostgreSQL row locking (FOR UPDATE)

## Rationale

Prevents:

- duplicate financial operations
- race conditions
- inconsistent retries

## Trade-offs

- Requires additional storage (idempotency_keys)
- Slight latency increase due to locking
