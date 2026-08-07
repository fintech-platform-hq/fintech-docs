# ADR-0005: Use Expense and Income as Transaction Types

## Status

Accepted

## Context

The product is a personal-finance application, not a bookkeeping or ledger interface.

Debit and credit introduce accounting and banking ambiguity for end users and do not match the current product model.

## Decision

Use the following transaction types throughout the current public API and domain:

- `expense`
- `income`

Money remains represented as a positive `amountMinor`.

Transaction type determines semantic direction:

- `expense`: outgoing personal-finance transaction
- `income`: incoming personal-finance transaction

The iOS client maps these values directly to user-facing labels:

- Expense -> visual `-R$`
- Income -> visual `+R$`

The visual sign is presentation-only and is not encoded into `amountMinor`.

## Consequences

Pros:

- Clearer product and domain language
- Direct iOS to API mapping
- Less translation logic between client and backend
- Less ambiguity for a personal-finance user

Trade-off:

- If the product later supports transfers, refunds, ledger postings, or double-entry accounting, transaction modeling may need to evolve beyond a simple `expense` / `income` enum.

This future modeling work is not decided or implemented by this ADR.
