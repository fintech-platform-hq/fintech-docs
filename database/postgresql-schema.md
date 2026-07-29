# PostgreSQL Schema — Fintech Platform

## 1. Goal

This document defines the relational data model for the Fintech Platform MVP.

The schema is designed to support:

- third normal form (3NF)
- immutable financial transactions
- derived account balances
- offline-first mobile synchronization
- idempotent financial writes
- account linking across identity providers
- secure refresh token management

---

## 2. Design Principles

### 2.1 Backend as Source of Truth

The PostgreSQL database is the authoritative source of truth for financial data.

The iOS app may cache data locally, but it must reconcile with the backend before considering financial operations confirmed.

### 2.2 Transactions Are Immutable

Confirmed financial transactions must not be updated in place.

If a transaction needs correction, the system must create a compensating transaction instead of mutating the original record.

### 2.3 Balance Is Derived

Account balance must not be stored directly in the `accounts` table.

Balance is derived from confirmed transactions:

```sql
SUM(
  CASE
    WHEN type = 'credit' THEN amount_minor
    WHEN type = 'debit' THEN -amount_minor
  END
)
```

### 2.4 Money Representation

Financial amounts are stored as integer minor units.

Example:

- BRL 10.50 → `1050`
- USD 25.99 → `2599`

This avoids floating-point rounding errors and simplifies synchronization across backend and mobile clients.

---

## 3. PostgreSQL Extensions

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```

---

## 4. Enum Types

```sql
CREATE TYPE account_type AS ENUM (
  'checking',
  'savings',
  'cash',
  'credit_card',
  'investment',
  'other'
);

CREATE TYPE transaction_type AS ENUM (
  'debit',
  'credit'
);

CREATE TYPE transaction_status AS ENUM (
  'confirmed',
  'voided'
);

CREATE TYPE auth_provider AS ENUM (
  'apple',
  'google',
  'email'
);

CREATE TYPE idempotency_status AS ENUM (
  'processing',
  'completed',
  'failed'
);

CREATE TYPE device_platform AS ENUM (
  'ios',
  'web'
);
```

---

## 5. Tables

### 5.1 users

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  email TEXT UNIQUE,
  display_name TEXT,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT users_email_not_blank
    CHECK (email IS NULL OR length(trim(email)) > 0)
);
```

#### Notes

- `email` is nullable because Apple login may hide email after first authorization.
- Identity is not stored directly in `users`; it is normalized into `auth_identities`.

---

### 5.2 auth_identities

```sql
CREATE TABLE auth_identities (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  provider auth_provider NOT NULL,
  provider_subject TEXT NOT NULL,

  email TEXT,
  email_verified BOOLEAN NOT NULL DEFAULT false,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT auth_identities_provider_subject_not_blank
    CHECK (length(trim(provider_subject)) > 0),

  CONSTRAINT auth_identities_unique_provider_subject
    UNIQUE (provider, provider_subject),

  CONSTRAINT auth_identities_unique_user_provider
    UNIQUE (user_id, provider)
);
```

#### Purpose

Supports account linking.

A single user may authenticate through multiple providers, but each external identity must map to exactly one internal user.

---

### 5.3 user_devices

```sql
CREATE TABLE user_devices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  platform device_platform NOT NULL,
  device_identifier_hash TEXT NOT NULL,
  device_name TEXT,

  last_seen_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT user_devices_identifier_not_blank
    CHECK (length(trim(device_identifier_hash)) > 0),

  CONSTRAINT user_devices_unique_user_device
    UNIQUE (user_id, device_identifier_hash)
);
```

#### Purpose

Used for refresh token binding, session tracking, and future device-level revocation.

The raw device identifier must not be stored.

---

### 5.4 refresh_tokens

```sql
CREATE TABLE refresh_tokens (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  device_id UUID NOT NULL REFERENCES user_devices(id) ON DELETE CASCADE,

  token_hash TEXT NOT NULL UNIQUE,

  replaced_by_token_id UUID REFERENCES refresh_tokens(id) ON DELETE SET NULL,

  issued_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  expires_at TIMESTAMPTZ NOT NULL,
  revoked_at TIMESTAMPTZ,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT refresh_tokens_hash_not_blank
    CHECK (length(trim(token_hash)) > 0),

  CONSTRAINT refresh_tokens_expiration_valid
    CHECK (expires_at > issued_at)
);
```

#### Purpose

Supports refresh token rotation.

Only token hashes are persisted. Raw refresh tokens must never be stored.

---

### 5.5 accounts

```sql
CREATE TABLE accounts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  name TEXT NOT NULL,
  type account_type NOT NULL,
  currency CHAR(3) NOT NULL DEFAULT 'BRL',

  archived_at TIMESTAMPTZ,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT accounts_name_not_blank
    CHECK (length(trim(name)) > 0),

  CONSTRAINT accounts_currency_iso_like
    CHECK (currency ~ '^[A-Z]{3}$'),

  CONSTRAINT accounts_unique_name_per_user
    UNIQUE (user_id, name)
);
```

#### Notes

- `currency` stays on the account because each account operates in one currency.
- Multi-currency transfers are out of scope for the MVP.

---

### 5.6 categories

```sql
CREATE TABLE categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  name TEXT NOT NULL,
  parent_category_id UUID REFERENCES categories(id) ON DELETE SET NULL,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT categories_name_not_blank
    CHECK (length(trim(name)) > 0),

  CONSTRAINT categories_unique_name_per_user
    UNIQUE (user_id, name),

  CONSTRAINT categories_no_self_parent
    CHECK (id IS DISTINCT FROM parent_category_id)
);
```

#### Notes

- Categories belong to users.
- Optional parent category allows simple grouping without violating 3NF.

---

### 5.7 transactions

```sql
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  account_id UUID NOT NULL REFERENCES accounts(id) ON DELETE RESTRICT,
  category_id UUID REFERENCES categories(id) ON DELETE SET NULL,

  type transaction_type NOT NULL,
  status transaction_status NOT NULL DEFAULT 'confirmed',

  amount_minor BIGINT NOT NULL,
  currency CHAR(3) NOT NULL,

  description TEXT,

  occurred_at TIMESTAMPTZ NOT NULL,
  confirmed_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  client_mutation_id UUID NOT NULL,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT transactions_amount_positive
    CHECK (amount_minor > 0),

  CONSTRAINT transactions_currency_iso_like
    CHECK (currency ~ '^[A-Z]{3}$'),

  CONSTRAINT transactions_description_not_blank
    CHECK (description IS NULL OR length(trim(description)) > 0),

  CONSTRAINT transactions_unique_client_mutation
    UNIQUE (user_id, client_mutation_id)
);
```

#### Purpose

Stores confirmed financial events.

#### Important

Transactions are append-only at the domain level.

Do not implement generic update endpoints for this table.

---

### 5.8 idempotency_keys

```sql
CREATE TABLE idempotency_keys (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  idempotency_key TEXT NOT NULL,
  request_method TEXT NOT NULL,
  request_path TEXT NOT NULL,
  request_hash TEXT NOT NULL,

  status idempotency_status NOT NULL DEFAULT 'processing',

  response_status INTEGER,
  response_body JSONB,

  locked_until TIMESTAMPTZ,
  completed_at TIMESTAMPTZ,

  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),

  CONSTRAINT idempotency_key_not_blank
    CHECK (length(trim(idempotency_key)) > 0),

  CONSTRAINT idempotency_method_not_blank
    CHECK (length(trim(request_method)) > 0),

  CONSTRAINT idempotency_path_not_blank
    CHECK (length(trim(request_path)) > 0),

  CONSTRAINT idempotency_hash_not_blank
    CHECK (length(trim(request_hash)) > 0),

  CONSTRAINT idempotency_unique_key_scope
    UNIQUE (user_id, idempotency_key, request_method, request_path)
);
```

#### Purpose

Prevents duplicate financial writes during retries, offline sync, flaky networks, and concurrent client requests.

---

## 6. Indexes

```sql
CREATE INDEX idx_auth_identities_user_id
  ON auth_identities(user_id);

CREATE INDEX idx_user_devices_user_id
  ON user_devices(user_id);

CREATE INDEX idx_refresh_tokens_user_device
  ON refresh_tokens(user_id, device_id);

CREATE INDEX idx_refresh_tokens_expires_at
  ON refresh_tokens(expires_at);

CREATE INDEX idx_accounts_user_id
  ON accounts(user_id);

CREATE INDEX idx_categories_user_id
  ON categories(user_id);

CREATE INDEX idx_transactions_user_account_occurred_at
  ON transactions(user_id, account_id, occurred_at DESC);

CREATE INDEX idx_transactions_user_occurred_at
  ON transactions(user_id, occurred_at DESC);

CREATE INDEX idx_transactions_category_id
  ON transactions(category_id);

CREATE INDEX idx_idempotency_keys_user_created_at
  ON idempotency_keys(user_id, created_at DESC);

CREATE INDEX idx_idempotency_keys_status_locked_until
  ON idempotency_keys(status, locked_until);
```

---

## 7. Account Balance Query

```sql
SELECT
  account_id,
  COALESCE(
    SUM(
      CASE
        WHEN type = 'credit' THEN amount_minor
        WHEN type = 'debit' THEN -amount_minor
      END
    ),
    0
  ) AS balance_minor
FROM transactions
WHERE
  user_id = $1
  AND account_id = $2
  AND status = 'confirmed'
GROUP BY account_id;
```

---

## 8. 3NF Validation

### users

Every non-key attribute depends only on `users.id`.

### auth_identities

Provider-specific identity data is separated from users.

This avoids storing multiple provider identities in the `users` table.

### accounts

Account metadata depends only on `accounts.id`.

Balance is not stored because it is derived from transactions.

### categories

Category data is separated from transactions.

This avoids repeating category names across financial events.

### transactions

Each transaction stores only the facts of a financial event.

Derived values such as balance, monthly summaries, or category totals are not stored.

### idempotency_keys

Idempotency metadata is separated from transactions because idempotency belongs to API request processing, not to the financial event itself.

---

## 9. MVP Non-Goals

The schema intentionally does not support:

- external bank integrations
- double-entry accounting
- multi-currency conversion
- card authorization lifecycle
- investment products
- shared family accounts
- invoice processing

These features would require additional modeling and should not be added before the MVP proves the core financial transaction flow.
