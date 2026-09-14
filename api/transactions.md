# Transactions API

The canonical public contract is [fintech-api/openapi.yaml](https://github.com/fintech-platform-hq/fintech-api/blob/main/openapi.yaml). This page keeps only shared behavior.

`POST /transactions` requires a Bearer access token and a UUID `Idempotency-Key`. The request uses camelCase fields, positive integer `amountMinor`, `expense` or `income`, and a stable `clientMutationId`.

The backend derives `userId` from the JWT, verifies account and category ownership, and requires transaction currency to match the account. A successful write returns the canonical transaction response. Reusing a key with the same complete payload replays the stored response; a different payload returns `409 Conflict`.

Authentication failures return `401`. A missing account or category, or one that belongs to another user, returns `404`. A currency that does not match the account returns `400`.

The transaction and idempotency record are committed atomically in PostgreSQL. The request hash includes the complete logical payload. Client retry behavior and UI details remain documented in fintech-ios.
