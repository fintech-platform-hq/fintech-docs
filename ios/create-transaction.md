# iOS Create Transaction

## Purpose

Allow a personal-finance user to record an Expense or Income from the iOS client.

This is a high-level platform document. Pixel-level SwiftUI and accessibility details belong in the `fintech-ios` repository.

## Implemented UI Semantics

- Expense displays as `-R$`
- Income displays as `+R$`
- The amount entry is BRL-focused in the current flow
- The user enters digits; currency symbols, separators, and signs are presentation
- The user can optionally enter a description

## Domain Semantics

The sign is presentation-only.

Expense example:

```json
{
  "type": "expense",
  "amountMinor": 5555
}
```

Income example:

```json
{
  "type": "income",
  "amountMinor": 5555
}
```

`amountMinor` remains positive for both transaction types. `TransactionType` carries the semantic direction.

## Implemented Flow

1. User selects Expense or Income.
2. User enters an amount.
3. User optionally enters a description.
4. iOS formats the amount visually as `-R$` or `+R$`.
5. iOS generates an `Idempotency-Key`.
6. iOS generates a `clientMutationId` for the logical operation.
7. iOS sends `POST /transactions` to the backend.
8. Backend validates and persists the transaction.
9. Backend returns the canonical transaction response.
10. iOS renders success, validation, network, conflict, cancellation, or unexpected error state.

## Retry Semantics

For an unchanged retry after an ambiguous failure, the iOS view model retains the pending request and idempotency key. Editing the form creates a new logical operation with a new idempotency key and client mutation ID.

## Planned / Not Implemented Here

This central docs repository does not define detailed native layout, typography, keyboard mechanics, or internal SwiftUI component structure. Those details are owned by `fintech-ios`.
