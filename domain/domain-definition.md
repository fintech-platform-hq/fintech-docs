# Domain Definition

This page records the early transaction implementation before I1–I5. For the approved conceptual baseline see the [functional model](functional-model.md). The [I5 delivery record](../architecture/i5-delivery.md) and [current implementation scope](functional-traceability.md#implementation-and-operational-validation) document later capabilities and outstanding checks.

## Implemented domain

- `User` is the internal principal identified by `users.id`.
- A user may authenticate with email/password, Apple, or both.
- Apple `sub` is an external identity. Matching email never silently merges users.
- `Account` and `Category` belong to a user and are checked on every financial write.
- `Transaction` is a confirmed backend financial event. `expense` and `income` use positive `amountMinor`; direction is carried by `type`.
- A refresh session belongs to a user and rotates on refresh.

Apple linking is explicit, optional, and supported by both the backend and iOS client after password authentication.

## Implemented flow

Authentication establishes one Lenver session. Authenticated account and transaction requests derive ownership from the JWT principal; client payloads cannot select another user. PostgreSQL is the source of truth for confirmed financial writes.

## iOS scope at the time of this snapshot

At this stage, the client had no account selection/creation UI, category UI, history, balance view, or offline reconciliation. Some of these capabilities already had backend schema support at the time.
