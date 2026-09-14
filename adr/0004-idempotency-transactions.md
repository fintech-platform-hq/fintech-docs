# ADR 0004 - Idempotency for Transactions

## Status

Accepted and implemented in the authenticated transaction API.

## Decision

Each logical `POST /transactions` operation uses a stable UUID `Idempotency-Key`, stable complete payload (including `clientMutationId`), deterministic request hashing, and an atomic PostgreSQL transaction. Idempotency keys are scoped by authenticated user.

Identical persisted requests replay the canonical response. A different payload with the same user and key returns `409 Conflict`.

Concurrency of the first write for a new key requires explicit test coverage and validation evidence.

The backend implementation and its concurrency evidence remain the source of truth for locking details and deployment status.
