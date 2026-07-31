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
