# Transactions API

This document describes the currently implemented `POST /transactions` endpoint in `fintech-api`.

## Endpoint

```http
POST /transactions
```

## Implemented Purpose

Create or replay a personal-finance transaction in an idempotent way.

The current public contract uses:

- `expense`: outgoing personal-finance transaction
- `income`: incoming personal-finance transaction

`amountMinor` is always a positive integer. Transaction direction is represented by `type`, not by a negative amount.

## Headers

The current endpoint does not require authentication.

```http
Content-Type: application/json
Idempotency-Key: <uuid>
```

`Idempotency-Key` is required and must be a UUID.

## Request Body

The request body is defined by `CreateTransactionDto` in the backend.

```json
{
  "accountId": "11111111-1111-4111-8111-111111111111",
  "categoryId": null,
  "type": "expense",
  "amountMinor": 5555,
  "currency": "BRL",
  "description": "Lunch",
  "occurredAt": "2026-01-01T12:00:00Z",
  "clientMutationId": "00000000-0000-4000-8000-000000000002"
}
```

### Fields

| Field | Required | Current validation |
| --- | --- | --- |
| `accountId` | Yes | UUID |
| `categoryId` | No | UUID when present; may be omitted or `null` |
| `type` | Yes | `expense` or `income` |
| `amountMinor` | Yes | Integer, minimum `1` |
| `currency` | Yes | Three uppercase ASCII letters such as `BRL` |
| `description` | No | String when present |
| `occurredAt` | Yes | Strict ISO 8601 timestamp with `T` separator and explicit timezone |
| `clientMutationId` | Yes | UUID; stable across retries of one logical client operation |

## Response: 201 Created

The backend returns `201` both for a new transaction and for a replay where the same `Idempotency-Key` is reused with the same complete payload after the original response has been persisted.

```json
{
  "id": "7ce3515c-9f07-4510-80de-20f4f94b6033",
  "accountId": "11111111-1111-4111-8111-111111111111",
  "categoryId": null,
  "type": "expense",
  "amountMinor": 5555,
  "currency": "BRL",
  "description": "Lunch",
  "occurredAt": "2026-01-01T12:00:00.000Z",
  "createdAt": "2026-01-01T12:00:01.000Z"
}
```

Income uses the same positive amount convention:

```json
{
  "accountId": "11111111-1111-4111-8111-111111111111",
  "categoryId": null,
  "type": "income",
  "amountMinor": 5555,
  "currency": "BRL",
  "description": "Salary",
  "occurredAt": "2026-01-01T12:00:00Z",
  "clientMutationId": "00000000-0000-4000-8000-000000000003"
}
```

## Response: 400 Bad Request

Returned for invalid headers or invalid request bodies. The backend currently uses NestJS error envelopes.

Examples:

```json
{
  "message": "Idempotency key required",
  "error": "Bad Request",
  "statusCode": 400
}
```

```json
{
  "message": [
    "type must be one of the following values: expense, income"
  ],
  "error": "Bad Request",
  "statusCode": 400
}
```

## Response: 409 Conflict

Returned when the same `Idempotency-Key` is reused with a different complete request payload.

```json
{
  "message": "Idempotency key was already used with a different request",
  "error": "Conflict",
  "statusCode": 409
}
```

## Response: 5xx

Unexpected server or persistence failures use the current NestJS error envelope. There is no custom public error code contract for these responses in the implemented API.

## Implemented Idempotency Behavior

Confirmed behavior:

- Same `Idempotency-Key` + same complete payload after a response is persisted -> returns the stored response with `201`
- Same `Idempotency-Key` + different complete payload -> `409 Conflict`
- Transaction insert and idempotency response storage occur inside one PostgreSQL transaction
- Request hashing includes `accountId`, `categoryId`, `type`, `amountMinor`, `currency`, `description`, `occurredAt`, and `clientMutationId`
- Stored responses are replayed from `idempotency_keys.response`

Compatibility behavior:

- The current API accepts only `expense` and `income`.
- The service still recognizes historical request hashes that used `debit`/`credit` so previously persisted idempotency records can replay after the domain rename.

Known gap / future hardening:

- Concurrent first requests with the same `Idempotency-Key` require explicit concurrency validation and hardening evidence. The current implementation uses a PostgreSQL advisory transaction lock plus `SELECT ... FOR UPDATE`, but this documentation does not claim a verified concurrent first-write guarantee.

## References

- [OpenAPI contract](openapi/transactions.yaml)
- [Backend idempotency strategy](../backend/idempotency-strategy.md)
- [PostgreSQL schema](../database/postgresql-schema.md)
- [ADR-0003: Idempotency for Financial Writes](../adr/0003-idempotency-for-writes.md)
- [ADR-0005: Use Expense and Income as Transaction Types](../adr/0005-personal-finance-transaction-types.md)
