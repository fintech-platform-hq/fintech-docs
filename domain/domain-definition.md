# Domain Definition — Fintech Platform
# Domain Definition — Fintech Platform

## 1. Objective

Design a financial platform capable of managing user accounts, transactions, and cashflow with strong consistency guarantees and offline-first mobile support.

---

## 2. Core Domain

### Entities

* User
* Account
* Transaction
* Category

### Responsibilities

* User owns multiple accounts
* Account aggregates transactions
* Transaction represents a financial event (immutable)
* Category classifies transactions

---

## 3. Domain Invariants

### Critical (must never be violated)

1. Transactions are immutable after confirmation
2. Account balance is derived, never stored directly
3. Every transaction must belong to exactly one account
4. All financial operations must be idempotent
5. Monetary values must use fixed precision (decimal)

---

### High Priority

6. Transactions must be timestamped (event ordering)
7. A transaction cannot exist without a valid user context
8. Duplicate transactions must be prevented at system level

---

### Medium Priority

9. Categories are optional but normalized
10. Users may have multiple accounts with independent balances

---

## 4. Data Model (3NF)

### users

* id (UUID, PK)
* email (unique)
* created_at

---

### accounts

* id (UUID, PK)
* user_id (FK → users.id)
* name
* type
* created_at

---

### transactions

* id (UUID, PK)
* account_id (FK → accounts.id)
* amount (DECIMAL(12,2))
* type (debit | credit)
* description
* created_at

---

### categories

* id (UUID, PK)
* name

---

### transaction_categories

* transaction_id (FK → transactions.id)
* category_id (FK → categories.id)

---

## 5. Single Source of Truth (SSoT)

* Backend API is the only source of truth
* Mobile app acts as cache + UI layer
* Redis is used only for performance optimization

---

## 6. Critical Flow — Offline Transaction Sync

### Step 1 — Mobile (Offline)

* User creates transaction
* Stored locally with:

  * temporary ID
  * status: pending_sync

---

### Step 2 — Sync Trigger

* Network becomes available
* Background task sends transaction to API

---

### Step 3 — Backend Processing

* Validate authentication (JWT)
* Check idempotency (request_id)
* Persist transaction
* Return canonical transaction ID

---

### Step 4 — Reconciliation

* Mobile replaces temporary ID
* Marks transaction as synced
* Updates local state

---

## 7. Engineering Risks

### Critical

* Duplicate transactions (lack of idempotency)
* Inconsistent balance calculation

### High

* Race conditions in concurrent writes
* Offline/online conflict resolution

---

## 8. Non-Goals (MVP)

* External bank integrations
* Real-time market data
* Complex financial instruments

---

## 9. Success Criteria

* Transactions remain consistent under offline/online scenarios
* No duplicate financial operations
* Balance is always correctly derived
* System behaves deterministically under concurrency

