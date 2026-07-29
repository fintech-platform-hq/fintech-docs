# ADR 0001 — Backend as Source of Truth

## Status
Accepted

## Context

The system must support offline-first mobile usage, background sync, and unreliable network conditions.

Allowing the mobile client to act as the source of truth would lead to:

- inconsistent financial data
- duplicated transactions
- race conditions during synchronization

## Decision

The backend API will be the single source of truth (SSoT) for all financial data.

The mobile application will act as:

- cache layer
- UI layer
- temporary state holder

All financial operations must be confirmed by the backend before being considered final.

## Consequences

### Positive

- Strong consistency of financial data
- Simplified conflict resolution
- Centralized validation and business rules

### Negative

- Requires synchronization layer in mobile
- Increased backend responsibility
- Slightly higher latency for confirmation

## Alternatives Considered

### Mobile as source of truth

Rejected due to high risk of inconsistency and data divergence.

### Hybrid model

Rejected due to increased complexity without strong guarantees.
