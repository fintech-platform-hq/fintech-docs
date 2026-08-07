# ADR 0001 — Backend as Source of Truth

## Status
Accepted

## Context

The platform target includes offline-first mobile usage, background sync, and unreliable network conditions. The current implemented iOS slice already treats the backend as authoritative for confirmed transaction writes.

Allowing the mobile client to act as the source of truth would lead to:

- inconsistent financial data
- duplicated transactions
- race conditions during synchronization

## Decision

The backend API will be the single source of truth (SSoT) for all financial data.

The mobile application will act as:

- cache layer when local persistence is introduced
- UI layer
- temporary state holder

All financial operations must be confirmed by the backend before being considered final.

## Consequences

### Positive

- Strong consistency of financial data
- Simplified conflict resolution
- Centralized validation and business rules

### Negative

- Requires synchronization layer in mobile for the future offline-first target
- Increased backend responsibility
- Slightly higher latency for confirmation

## Alternatives Considered

### Mobile as source of truth

Rejected due to high risk of inconsistency and data divergence.

### Hybrid model

Rejected due to increased complexity without strong guarantees.
