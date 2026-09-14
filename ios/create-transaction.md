# iOS Create Transaction

The iOS client records Expense or Income through the authenticated backend. It sends positive integer `amountMinor`, a user-owned account ID, optional category ID, and one stable idempotency key per logical operation.

The backend is authoritative for ownership, validation, persistence, and confirmed results. iOS shows `-R$` for Expense and `+R$` for Income; the sign is presentation only.

The current composition uses a disposable demo account ID and has no account selection or account-provisioning UI. Native layout, accessibility, and SwiftUI implementation details remain in fintech-ios.
