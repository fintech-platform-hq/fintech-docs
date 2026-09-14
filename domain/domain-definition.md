# Domain Definition

## Implemented domain

- `User` is the internal principal identified by `users.id`.
- A user may authenticate with email/password, Apple, or both.
- Apple `sub` is an external identity. Matching email never silently merges users.
- `Account` and `Category` belong to a user and are checked on every financial write.
- `Transaction` is a confirmed backend financial event. `expense` and `income` use positive `amountMinor`; direction is carried by `type`.
- A refresh session belongs to a user and rotates on refresh.

Apple linking is explicit and backend-supported. The iOS linking experience remains pending.

## Implemented flow

Authentication establishes one Fintech session. Authenticated account and transaction requests derive ownership from the JWT principal; client payloads cannot select another user. PostgreSQL is the source of truth for confirmed financial writes.

## Not implemented in the iOS product

The current client has no account selection/creation UI, category UI, history, balance view, or offline reconciliation. These are future product work, even where backend schema support already exists.
