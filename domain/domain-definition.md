# Domain Definition - Fintech Platform

## Product Definition

Fintech Platform is a personal finance management product for recording, organizing, and understanding income and expenses.

The current implemented slice is intentionally narrow: an iOS client creates a transaction, the backend validates and persists it, and PostgreSQL is the source of truth for confirmed financial writes.

## Current MVP Domain

### Transaction

A transaction is a confirmed personal-finance event persisted by the backend.

Current transaction types:

- `expense`: outgoing financial transaction
- `income`: incoming financial transaction

The public API and iOS client both use these values directly. There is no current public translation layer from `debit`/`credit`.

### amountMinor

Money is represented as positive integer minor units.

Examples:

- `R$ 55,55` -> `amountMinor: 5555`
- Expense display `-R$ 55,55` -> API payload `{ "type": "expense", "amountMinor": 5555 }`
- Income display `+R$ 55,55` -> API payload `{ "type": "income", "amountMinor": 5555 }`

The sign shown in the iOS UI is presentation-only. Transaction direction is carried by `type`.

### IdempotencyKey

An idempotency key identifies one logical write operation at the API boundary.

Current behavior:

- same key + same complete payload after persistence -> replay stored response
- same key + different complete payload -> conflict
- the transaction and stored idempotency response are written in one database transaction

Concurrent first-write hardening is a known area for validation and should not be overstated as proven behavior.

## Current MVP Invariants

- Backend API is the source of truth for confirmed financial writes.
- `type` is exactly `expense` or `income`.
- `amountMinor` is always a positive integer.
- Currency is represented as a three-letter uppercase code.
- Transaction creation requires an idempotency key.
- Replays must use the same complete request payload, including `clientMutationId`.
- Confirmed transaction responses use the canonical backend response shape.

## Current End-to-End Flow

1. User selects Expense or Income in the iOS Create Transaction flow.
2. User enters a BRL amount and optional description.
3. iOS renders `-R$` for Expense and `+R$` for Income.
4. iOS generates a positive `amountMinor`, a `clientMutationId`, and an `Idempotency-Key`.
5. iOS sends `POST /transactions`.
6. NestJS validates the request and idempotency key.
7. PostgreSQL persists the transaction and idempotency response.
8. The API returns the canonical transaction response.
9. iOS transitions to success or error state based on the backend response.

## Target Domain / Future Evolution

The following concepts are part of the target product architecture, not the current production schema unless a future document marks them as implemented:

- User
- Account ownership and account metadata
- Category ownership and category hierarchies
- Derived account balances
- Authentication identities
- Refresh tokens and device-bound sessions
- Offline-first sync and reconciliation
- Redis or other caching infrastructure

Target balance semantics should use the current domain language:

- `income` contributes positively
- `expense` contributes negatively

`amountMinor` should remain positive; balance calculations apply direction based on transaction type.

## Current Non-Goals

The implemented MVP slice does not currently include:

- public authentication or JWT enforcement
- user-owned account tables with foreign keys
- category ownership tables
- refresh token storage
- Redis
- offline-first synchronization
- external bank integrations
- transfers, refunds, or double-entry ledger postings
