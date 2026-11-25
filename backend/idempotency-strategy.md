# Backend Idempotency Strategy

The canonical implementation is in `lenver-backend/src/modules/transactions` and PostgreSQL. This page records the shared rule: one UUID `Idempotency-Key` and one complete request payload identify one logical financial write for one authenticated user.

The backend hashes the normalized payload, uses a transaction-scoped advisory lock keyed by user and idempotency key, checks the stored row, and commits the transaction plus replay response atomically. Same key and payload replays the canonical response; a changed payload returns `409 Conflict`.

The API repository owns SQL, migration, locking, retention, and concurrency-test details. This shared document does not claim production deployment or broader offline-sync guarantees.
