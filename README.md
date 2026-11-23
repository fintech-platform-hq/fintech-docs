# Fintech Platform - Technical Docs

Shared architectural and product documentation for fintech-api and fintech-ios.

## Repositories

- Backend API: https://github.com/fintech-platform-hq/fintech-api
- iOS client: https://github.com/fintech-platform-hq/fintech-ios
- Documentation: https://github.com/fintech-platform-hq/fintech-docs

## Implemented now

### Backend

- Email/password registration and login with JWT access tokens and rotating refresh sessions.
- Apple sign-in at `POST /auth/apple` and explicit linking at `POST /auth/apple/link`.
- Users, user-owned accounts and categories, ownership checks, and currency checks.
- Authenticated `GET /accounts` and `POST /accounts`.
- Authenticated, idempotent `POST /transactions`, scoped by user and backed by PostgreSQL.
- Apple refresh revalidation with temporary `503` handling and selective revocation.

The backend contract, migrations, Apple cryptographic details, and operational configuration remain canonical in [fintech-api](https://github.com/fintech-platform-hq/fintech-api).

### iOS

- One `AuthenticationSession` and one Fintech access/refresh-token store using Keychain.
- Email/password authentication and native Sign in with Apple infrastructure.
- Optional Apple account linking after password login, including `ACCOUNT_LINK_REQUIRED` handling and explicit consent.
- 32-byte Base64URL nonce, single Apple authorization POST, and no persistence of Apple identity tokens or authorization codes.
- Safe refresh behavior: `401` ends the session, `503` preserves credentials, and ambiguous transport failures require reauthentication.
- Create Transaction flow using positive integer minor units and backend idempotency.

Implementation status reflects the current repository code. The preceding iOS checkpoint was validated locally on 2026-09-14 (focused and full automated validation); Apple real-device use was not validated, and Apple production deployment/configuration is not proven.

## Current boundaries

Apple linking is implemented in the iOS client and backend. Apple capability/provisioning and real-device validation are operational prerequisites, not evidence of implementation. Production migration, environment configuration, and deployment must be verified separately.

The iOS Debug MVP opt-in includes the personal balance, account history, dated balance checks, retroactive expense recovery, audited corrections, invalidations, check replacements, and opening balance review. Categories and general offline reconciliation are outside that flow. Server-to-server Apple notifications, Redis, workers, schedulers, and token blacklists remain outside the MVP.

## Key documents

- [I4 audited corrections and check replacement](architecture/i4-delivery.md)
- [I5 opening balance correction and journey verification](architecture/i5-delivery.md)
- [I3 retroactive expense recovery](architecture/i3-delivery.md)
- [Authentication architecture](architecture/authentication.md)
- [Transactions API](api/transactions.md)
- [Current schema and ownership](database/postgresql-schema.md)
- [Domain definition](domain/domain-definition.md)
- [Backend idempotency strategy](backend/idempotency-strategy.md)
- [iOS Create Transaction](ios/create-transaction.md)
- [ADR-0005: Expense and Income](adr/0005-personal-finance-transaction-types.md)
