# Transactions API — Fintech Platform

## 1. Endpoint

POST /transactions

---

## 2. Purpose

Create a financial transaction in a safe and idempotent way.

This endpoint must:

- prevent duplicate operations
- validate business rules
- persist immutable financial events

---

## 3. Headers

```
Authorization: Bearer <access_token>
Idempotency-Key: <uuid>
Content-Type: application/json
```

---

## 4. Request Body

```json
{
  "accountId": "uuid",
  "categoryId": "uuid | null",
  "type": "debit",
  "amountMinor": 1500,
  "currency": "BRL",
  "description": "Lunch",
  "occurredAt": "2026-01-01T12:00:00Z",
  "clientMutationId": "uuid"
}
```

---

## 5. Validation Rules

### Critical

- account must belong to authenticated user
- amountMinor > 0
- currency must match account currency
- clientMutationId must be unique per user

### High

- category must belong to user (if provided)
- occurredAt must be a valid ISO 8601 timestamp (UTC)
- occurredAt must not be in the future (optional rule)

---

## 6. Responses

### 201 Created

```json
{
  "id": "uuid",
  "accountId": "uuid",
  "type": "debit",
  "amountMinor": 1500,
  "currency": "BRL",
  "description": "Lunch",
  "occurredAt": "2026-01-01T12:00:00Z",
  "createdAt": "2026-01-01T12:00:01Z"
}
```

---

### 409 Conflict

#### Case 1: Idempotency key reused with different payload

```json
{
  "code": "IDEMPOTENCY_KEY_CONFLICT",
  "message": "Idempotency key already used with different payload"
}
```

---

### 400 Bad Request

- invalid amount
- invalid currency
- invalid payload

---

### 403 Forbidden

- account does not belong to user

---

## 7. Idempotency Behavior

- Same key + same payload → return same response
- Same key + different payload → 409
- Different key → new operation

---

## 8. Database Interaction

1. Validate idempotency key
2. BEGIN transaction
3. Check for existing idempotency key
4. Insert transaction
5. Store response in idempotency_keys
6. COMMIT transaction
7. Return response

---

## 9. Notes

- This endpoint must be atomic
- Must run inside a database transaction
- Must not allow partial writes
- Financial data must be immutable after creation

---

## 10. References

- OpenAPI: ./openapi/transactions.yaml
- Idempotency Strategy: ../backend/idempotency-strategy.md
- Database Schema: ../database/postgresql-schema.md
- ADR: ../adr/0003-idempotency-for-writes.md

---

## 11. Idempotency Guarantees

This endpoint guarantees:

- Same Idempotency-Key + same payload → same response (no duplicate writes)
- Same Idempotency-Key + different payload → 409 Conflict
- Concurrent requests with same key → only one transaction is persisted

Implementation details:

- Uses PostgreSQL transaction (BEGIN / COMMIT / ROLLBACK)
- Row-level locking via `SELECT ... FOR UPDATE`
- Request hashing to detect payload changes
- Response stored and replayed from `idempotency_keys`
