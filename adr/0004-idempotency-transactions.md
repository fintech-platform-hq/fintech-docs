# ADR 0004 - Idempotency for Transactions

## Status

Accepted

## Context

`POST /transactions` is the first financial write endpoint in the platform. Retrying the same logical operation must not create duplicate transactions after a response has already been persisted.

## Decision

The transactions endpoint implements idempotency using:

- `Idempotency-Key` HTTP header, currently required to be a UUID
- deterministic request hash comparison
- canonical response persistence in `idempotency_keys.response`
- PostgreSQL transaction wrapping transaction insert and idempotency response insert
- transaction-scoped advisory locking by idempotency key in the current backend implementation
- `SELECT ... FOR UPDATE` when reading an existing idempotency row

## Confirmed Behavior

- Same key + same complete payload after a response is persisted -> replay stored response
- Same key + different complete payload -> `409 Conflict`
- Missing or invalid idempotency key -> `400 Bad Request`

## Known Gap

This ADR does not claim a fully validated concurrent first-write guarantee.

`SELECT ... FOR UPDATE` does not lock a row that does not yet exist. The current backend also uses `pg_advisory_xact_lock(hashtext($1))`, but concurrent first-request behavior should be covered by explicit concurrency tests before the platform documentation describes it as a proven guarantee.

## Consequences

Positive:

- Replays can return the original canonical response.
- Payload mismatches are detected.
- Transaction and idempotency response storage are atomic in the current implementation.

Trade-offs:

- Additional persistence is required.
- Request hashing makes the full payload part of retry identity.
- Concurrency hardening needs explicit validation as the platform matures.
