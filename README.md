<<<<<<< HEAD
# Fintech Platform – Technical Documentation

This repository contains the architectural foundation and technical decisions behind the Fintech Platform.

It is the source of truth for system design, domain modeling, and backend behavior.

---

## System Overview

The platform is designed to support reliable financial operations with strong consistency guarantees.

Core principles:

- Idempotent transaction processing
- Explicit domain modeling
- PostgreSQL as the source of truth
- Backend-first architecture
- Production-oriented design decisions

---

## Repositories

- **API**  
  https://github.com/fintech-platform-hq/fintech-api  
  NestJS backend responsible for transaction processing and persistence

- **iOS Client (in progress)**  
  SwiftUI client consuming the API

---

## Documentation Structure

- `/domain`  
  Core business concepts and entities

- `/database`  
  Schema design and constraints

- `/api`  
  Endpoint contracts and behavior

- `/backend`  
  Implementation decisions and architecture

- `/adr`  
  Architectural Decision Records (trade-offs and reasoning)

---

## Key Decisions

- Idempotency enforced at the API level using request hashing
- PostgreSQL constraints used as a safety layer (not just validation)
- Stateless API with deterministic responses
- Separation between domain and transport layers

---

## Why this matters

This project focuses on problems commonly found in real financial systems:

- duplicate transaction prevention
- data consistency under retries
- predictable API behavior
- auditability and traceability

---

## Status

Current scope:

- Transaction creation endpoint
- Idempotency guarantees
- PostgreSQL persistence
- Production deployment (Render + Neon)

Next steps:

- balance computation
- transaction querying
- client integration (iOS)
=======
# Fintech Platform - Technical Docs

Fintech Platform is a personal finance platform for recording, organizing, and understanding income and expenses, backed by a production-deployed API and an iOS client.

This repository is the central architectural and product documentation for the platform. It describes the implemented end-to-end slice, the current API contract, and target architecture separately so readers can distinguish shipped behavior from planned evolution.

## Repositories

- Backend API: https://github.com/fintech-platform-hq/fintech-api
- iOS client: https://github.com/fintech-platform-hq/fintech-ios
- Platform documentation: https://github.com/fintech-platform-hq/fintech-docs

## Current Scope

### Backend: Implemented

- `POST /transactions`
- PostgreSQL persistence for transactions and idempotency records
- `expense` and `income` transaction types in the public contract and PostgreSQL enum
- Positive `amountMinor` representation for both expenses and income
- Request validation for UUIDs, transaction type, positive integer amount, uppercase currency, ISO/RFC3339 occurrence time, and `clientMutationId`
- `Idempotency-Key` header support
- Replay for the same idempotency key and same complete request payload after a response is persisted
- `409 Conflict` for the same idempotency key with a different complete request payload
- Request hashing for idempotency comparison
- Transaction insert and idempotency response storage in one PostgreSQL transaction
- Structured request and error logging with `X-Request-Id`
- Root endpoint and `GET /health` process liveness endpoint
- Docker support
- Production deployment on Render
- Managed PostgreSQL on Neon

### iOS: Implemented

- Create Transaction flow
- Expense / Income selection
- BRL amount entry using integer minor units
- Visual signed amount presentation: `-R$` for Expense and `+R$` for Income
- Optional description entry
- Request generation for the current backend contract
- `Idempotency-Key` generation and submission
- `clientMutationId` generation per logical operation
- Integration with the production backend base URL
- Validation, loading, error, retry, and success states covered by deterministic tests

## Current End-to-End Slice

User
-> Create Transaction on iOS
-> `POST /transactions`
-> NestJS API
-> PostgreSQL / Neon
-> canonical transaction response
-> iOS success state

The current slice records one personal finance transaction. `type` carries the semantic direction:

- `expense`: outgoing personal-finance transaction
- `income`: incoming personal-finance transaction

`amountMinor` is always positive. The iOS sign (`-R$` or `+R$`) is presentation-only and is not sent as part of the amount.

## Planned / Target Architecture

The broader platform direction includes authenticated users, owned accounts, categories, richer balance views, refresh-token based sessions, device-aware sync, and offline-first reconciliation. These are target architecture topics unless a document explicitly marks them as implemented.

Do not treat planned domain entities such as users, account ownership tables, category ownership, auth identities, refresh tokens, Redis, or offline sync as present in the current production schema or API.

## Key Documents

- [Transactions API](api/transactions.md)
- [OpenAPI transaction contract](api/openapi/transactions.yaml)
- [Current and target PostgreSQL schema](database/postgresql-schema.md)
- [Domain definition](domain/domain-definition.md)
- [Backend idempotency strategy](backend/idempotency-strategy.md)
- [iOS Create Transaction overview](ios/create-transaction.md)
- [ADR-0005: Use Expense and Income as Transaction Types](adr/0005-personal-finance-transaction-types.md)
>>>>>>> 5e86115 (docs: align platform documentation with current implementation)
