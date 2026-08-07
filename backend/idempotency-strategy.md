# Backend Idempotency Strategy

This document describes the currently implemented idempotency behavior for `POST /transactions`.

## Implemented Now

The transaction endpoint requires:

```http
Idempotency-Key: <uuid>
```

The key must be stable for every retry of the same logical transaction operation. The complete request payload must also remain stable, including `clientMutationId`.

## Request Hashing

The backend computes a deterministic SHA-256 request hash over:

- `accountId`
- `categoryId` normalized to `null` when absent
- `type`
- `amountMinor`
- `currency`
- `description` normalized to `null` when absent
- `occurredAt`
- `clientMutationId`

The current public transaction types are `expense` and `income`.

## Replay Semantics

Confirmed behavior:

- New key + valid payload -> insert transaction, store response, return `201`
- Same key + same complete payload after the response is persisted -> return stored response with `201`
- Same key + different complete payload -> return `409 Conflict`
- Missing key -> `400 Bad Request`
- Non-UUID key -> `400 Bad Request`

The stored response lives in `idempotency_keys.response` as JSONB.

## Database Transaction

For a new transaction, the current service performs the transaction insert and idempotency response insert inside one PostgreSQL transaction:

1. `BEGIN`
2. Acquire a transaction-scoped advisory lock using the idempotency key
3. Query `idempotency_keys` by `idempotency_key` with `FOR UPDATE`
4. Return stored response when the key already exists and the hash matches
5. Return conflict when the key already exists and the hash differs
6. Insert into `transactions`
7. Insert into `idempotency_keys`
8. `COMMIT`

On handled validation or persistence failures the service rolls back the database transaction before returning the error.

## Legacy Hash Compatibility

The API no longer accepts `debit` or `credit` in request bodies.

For replay compatibility, the service can compare the current request against a historical request hash where:

- `expense` maps to historical `debit`
- `income` maps to historical `credit`

This is only for existing idempotency records created before the rename. It is not a public request contract.

## Known Gap / Future Hardening

This documentation does not claim a verified guarantee that two concurrent first requests with the same `Idempotency-Key` can never both attempt work.

The current implementation uses `pg_advisory_xact_lock(hashtext($1))` before `SELECT ... FOR UPDATE`. That design is intended to serialize work for the same idempotency key, but the platform documentation should only claim concurrent first-write safety after the behavior is covered by explicit concurrency tests and operational evidence.

`SELECT ... FOR UPDATE` alone does not lock a row that does not yet exist, so row locking by itself is not a sufficient concurrency guarantee for the first write.

## Planned / Target Behavior

Future hardening should include:

- explicit concurrent first-request tests for the same idempotency key
- clear behavior for in-progress requests if asynchronous processing is introduced
- operational monitoring for idempotency conflicts and duplicate-key database errors
- a documented policy for expiration or retention of idempotency records
