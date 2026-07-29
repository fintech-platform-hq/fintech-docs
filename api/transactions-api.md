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
  "categoryId": "uuid",
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
  "code": "IDEMPOTENCY_KEY_CONFLICT"
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
2. Insert transaction
3. Store response in idempotency_keys
4. Return response

---

## 9. Notes

- This endpoint must be atomic
- Must run inside a database transaction
- Must not allow partial writes
