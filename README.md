# Lenver Docs

Technical documentation for Lenver, a personal finance project focused on recording transactions and understanding account history.

- [Lenver Backend](https://github.com/lenver-hq/lenver-backend)
- [Lenver iOS](https://github.com/lenver-hq/lenver-ios)

## Implementation status

The published source includes email/password authentication, Sign in with Apple and explicit account linking, authenticated and idempotent writes, and the I1–I5 financial journey. The iOS account journey is available through a Debug opt-in. It covers a BRL opening balance, account history, dated balance checks, retroactive expense recovery, and audited corrections.

Live deployment, legacy-data adoption, and Apple capability and physical-device checks have separate verification requirements. The [backend OpenAPI contract](https://github.com/lenver-hq/lenver-backend/blob/main/openapi.yaml) describes the published API.

## Documents

- [Authentication architecture](architecture/authentication.md)
- [Transactions API](api/transactions.md)
- [PostgreSQL schema and ownership](database/postgresql-schema.md)
- [Domain definition](domain/domain-definition.md)
- [Backend idempotency](backend/idempotency-strategy.md)
- [iOS transaction creation](ios/create-transaction.md)
- [I3 retroactive expense recovery](architecture/i3-delivery.md)
- [I4 audited corrections and check replacement](architecture/i4-delivery.md)
- [I5 opening balance corrections and verification](architecture/i5-delivery.md)

## Documentation policy

Private product decisions, planning, and evidence are maintained in Brazilian Portuguese in Confluence. This repository is intended for selected public documentation in English after editorial and sensitivity review. The approved bilingual glossary governs term equivalence. Full translation and subsequent publication are separate deliverables.
