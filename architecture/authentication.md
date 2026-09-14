# Authentication Architecture

## Implemented contract

Email/password registration and login issue the same Fintech `Bearer` access/refresh session model used by Apple sign-in. `users.id` is the internal principal. Apple `sub` is an external identity; email matching never merges accounts silently.

The backend exposes `POST /auth/register`, `POST /auth/login`, `POST /auth/apple`, `POST /auth/refresh`, `POST /auth/logout`, and explicit `POST /auth/apple/link`. Apple sign-in and linking accept the identity token, single-use authorization code, and exact nonce. Apple identity tokens are validated with RS256; the backend creates its client secret with ES256. Apple provider refresh tokens remain backend-only and encrypted.

The complete Apple contract is maintained in [`fintech-api/docs/specs/apple-authentication.md`](https://github.com/fintech-platform-hq/fintech-api/blob/main/docs/specs/apple-authentication.md).

The iOS client owns one `AuthenticationSession` and persists only Fintech access/refresh tokens in its existing Keychain mechanism. It does not persist Apple identity tokens or authorization codes. The native `SignInWithAppleButton` uses an injectable provider. The nonce is 32 random bytes encoded as unpadded Base64URL, passed unchanged to Apple and the backend. An ambiguous Apple POST is never replayed automatically.

## Refresh behavior

- `401`: invalidate the session and require login.
- Temporary `503`: preserve the existing Fintech credentials for a later attempt.
- Timeout or disconnection during refresh: treat the token as potentially consumed, do not retry it automatically (including after relaunch), and expose reauthentication required.

Single-flight refresh and generation checks prevent stale operations from changing a replacement session. A crash during an in-flight refresh may therefore require a new login.

## Pending and operational boundaries

When `POST /auth/apple` returns `ACCOUNT_LINK_REQUIRED`, iOS requires a password login before presenting an optional linking offer. Each linking attempt starts a new Apple authorization and sends its identity token, authorization code, and nonce to `POST /auth/apple/link` with the password session's Bearer token. Linking does not refresh or replay automatically; `204` and `401` preserve the Fintech session and tokens. Choosing “Agora não” abandons linking without invalidating the Fintech session. The client supports specific feedback and guards against concurrent or late results.

The linking flow is implemented and covered by deterministic local tests. Apple capability/provisioning and real-device validation remain pending. Apple production operation, migration application, environment secrets, and deployment require separate operational proof.

Server-to-server Apple notifications, Redis, schedulers, workers, and blacklists are outside the MVP.
