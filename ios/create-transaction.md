# iOS Create Transaction

The iOS client records Expense or Income through the authenticated backend. It sends positive integer `amountMinor`, a user-owned account ID, optional category ID, and one stable idempotency key per logical operation.

The backend is authoritative for ownership, validation, persistence, and confirmed results. iOS shows `-R$` for Expense and `+R$` for Income; the sign is presentation only.

The initial implementation used a disposable demo account ID. The published iOS source also includes a separate BRL account flow behind a Debug opt-in. Native layout, accessibility, and SwiftUI implementation details live in [Lenver iOS](https://github.com/lenver-hq/lenver-ios).
