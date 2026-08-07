# PostgreSQL Schema

This document separates the current production schema from target architecture.

## Current Production Schema

The current schema is defined by `fintech-api/db.sql` plus versioned migrations.

Implemented tables:

- `transactions`
- `idempotency_keys`

Implemented enum:

- `transaction_type`: `expense`, `income`

There are no current production tables for users, accounts, categories, auth identities, refresh tokens, devices, or Redis-backed state in this schema.

## Current Enum Types

```sql
CREATE TYPE transaction_type AS ENUM ('expense', 'income');
```

Semantics:

- `expense`: outgoing personal-finance transaction
- `income`: incoming personal-finance transaction

## Current Table: transactions

```sql
CREATE TABLE transactions (
  id uuid PRIMARY KEY,
  account_id uuid NOT NULL,
  category_id uuid NULL,
  type transaction_type NOT NULL,
  amount_minor integer NOT NULL CHECK (amount_minor > 0),
  currency char(3) NOT NULL CHECK (currency ~ '^[A-Z]{3}$'),
  description text NULL,
  occurred_at timestamptz NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
);
```

Indexes:

```sql
CREATE INDEX idx_transactions_account_id
ON transactions (account_id);

CREATE INDEX idx_transactions_occurred_at
ON transactions (occurred_at);
```

Notes:

- `amount_minor` is always positive.
- Transaction direction comes from `type`, not from a negative amount.
- `account_id` and `category_id` are UUID values in the current table, but there are no current foreign keys to account or category tables.
- `clientMutationId` is part of the API request hash but is not currently persisted as a transaction column.

## Current Table: idempotency_keys

```sql
CREATE TABLE idempotency_keys (
  id uuid PRIMARY KEY,
  idempotency_key uuid NOT NULL,
  request_hash text NOT NULL,
  response jsonb NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  UNIQUE (idempotency_key),
  UNIQUE (idempotency_key, request_hash)
);
```

Index:

```sql
CREATE INDEX idx_idempotency_keys_key
ON idempotency_keys (idempotency_key);
```

Notes:

- `response` stores the canonical API response used for replay.
- `request_hash` stores the hash of the complete request payload.
- Replays compare the incoming request hash with the stored hash.

## Migration: 20260805 Rename Transaction Types

`migrations/20260805_rename_transaction_type_values.sql` migrated the PostgreSQL enum and stored idempotency responses:

```sql
ALTER TYPE transaction_type RENAME VALUE 'debit' TO 'expense';
ALTER TYPE transaction_type RENAME VALUE 'credit' TO 'income';
```

The migration also rewrites `idempotency_keys.response->type`:

- `debit` -> `expense`
- `credit` -> `income`

This preserved existing transaction rows and stored replay responses while moving the public domain language to personal-finance terms.

## Current Balance Semantics

The current schema does not include an implemented balances table or account balance query.

When balance views are implemented, calculations should use the current type semantics:

```sql
SUM(
  CASE
    WHEN type = 'income' THEN amount_minor
    WHEN type = 'expense' THEN -amount_minor
  END
)
```

`amount_minor` remains positive in stored transactions.

## Target Schema - Planned / Not Implemented

The following schema areas are target architecture and are not part of the current production schema:

- `users`
- `accounts`
- `categories`
- `auth_identities`
- `user_devices`
- `refresh_tokens`
- richer idempotency state such as `processing`, `completed`, or expiration windows
- persisted `client_mutation_id` uniqueness scoped by user
- foreign keys from transactions to users, accounts, and categories
- derived account balance queries
- offline sync metadata

Target account and category modeling should be introduced only when authentication and account ownership are implemented in the backend contract.

## Target Design Principles

- PostgreSQL remains the source of truth for confirmed financial data.
- Money remains integer minor units, not floating point.
- `amount_minor` remains positive.
- Transaction direction is derived from `type`.
- Balances are derived from transactions rather than stored as mutable account state.
- Authentication and refresh-token storage should be documented as implemented only after the backend enforces them.

## Non-Goals for Current Schema

The current schema does not support:

- external bank integrations
- double-entry accounting
- transfers or ledger postings
- multi-currency conversion
- card authorization lifecycle
- shared family accounts
- invoice processing
