# PostgreSQL Schema

The canonical schema is `lenver-backend/db.sql` plus its versioned migrations. This page summarizes the shared architecture and does not duplicate the SQL.

## Implemented tables

- `users`: optional normalized email and password hash, with a deferred requirement for at least one authentication method.
- `accounts`: user-owned named accounts with a currency.
- `categories`: user-owned categories.
- `transactions`: user-owned financial records with composite ownership and account-currency foreign keys.
- `idempotency_keys`: request hash and canonical response, unique per user and idempotency key.
- `auth_identities`: Apple subject, metadata, encrypted provider refresh token, validation timestamp, and revocation state.
- `refresh_sessions`: hashed rotating Lenver refresh tokens, families, expiry, and revocation.

The `20260910_add_accounts_categories_ownership_auth.sql` and `20260911_add_apple_auth_identity_schema.sql` migrations define the ownership and Apple additions. Whether these migrations are applied in production is an operational question and is not inferred here.

## Invariants

Users are the internal principal. Apple subjects are external identities. Transactions require an owned account and matching currency; optional categories must also be owned by the user. Amounts remain positive integer minor units and `expense`/`income` determine direction.

## Future

Balances, history queries, offline metadata, and richer idempotency lifecycle state are not implemented. Redis and worker infrastructure are outside the MVP.
