# MF02 use-case catalog

Approved 2026-11-24 for MD-007 and MD-008. These are product objectives and acceptance examples. MF03.1 supplies later behavioral refinements, particularly the separation of lookup and resubmission in CU-020. See [behavior](behavior.md) and [case-by-case traceability](functional-traceability.md#case-traceability).

## Actors and boundary

| ID | Actor | Role |
| --- | --- | --- |
| AT01 | Person seeking access | Registers, signs in, proves email control or recovers access. An existing holder still needs to prove applicable authorization. |
| AT02 | Authorized account holder | Governs their Lenver account, manages their own finances and follows up on their own requests. The original deletion proof can confer lookup-only authority after session invalidation. |
| AT03 | Apple | Supplies Sign in with Apple and participates in linked authorization, applicable revocation and deletion. Identity recovery takes place at the provider. Relay or missing contact does not substitute for subject proof. |
| AT04 | Email service | Delivers verification or recovery messages. Lenver validates and scopes challenges and grants authorization. Provider and contract are open. |
| AT05 | Distribution operations | Collects release-gate evidence under A09. An external governance role without authority over in-product operations. |

The boundary contains Lenver iOS and Lenver services, including protected local storage and the database. AT01 and AT02 are roles of the same person at different times. Apple, email transport and system accessibility, appearance and language preferences are external. Beyond scope are option B, compensating adjustments, Open Finance, category UI, multiple active accounts, transfers, attachments/OCR, import, economic reversals, offline or cross-device synchronization, a multiple-write queue, Google sign-in, passkeys, an extra biometric lock, widgets, Siri, notifications and watchOS. App Intents and Shortcuts are future candidates with explicit in-app confirmation.

## Cross-cutting rules

- **RT01.** Validate holder, authorization and target on reads, writes, replays and history. Verified email gates password financial access. Valid Apple proof grants its own scope. Suppress sensitive content on expiration, sign-out or scope change.
- **RT02.** Protect original terms before transmission. Return the authorized original receipt before fresh-execution guards. Commit effect, trail, revision and receipt together. Refresh independently. Require definitive no-effect rejection before replacing an uncertain intent with edited terms. Deletion has its own guards.
- **RT03.** Maintain exact BRL, independent signed opening and reported balances, and 1–999,999,999 cents per transaction with direction by type. Include both T0 and cutoff for valid contributions. Preserve zone, civil day and known precision.
- **RT04.** Keep loading, confirmed empty, unavailable, uncertain, rejected, expired, suspended and stale preview distinguishable. A prior reading requires demonstrated origin, ownership, validity and time to appear as stale. Suppress invalid figures. Preserve authorized traceability. Telemetry excludes tokens, email, amounts, descriptions, references and financial IDs. Native controls, states announced without depending on color, AX5, 44 × 44 pt target, appearance and applicable accessibility preferences are UX01-17 criteria. Use en-US fallback and full pt-BR, preserving currency and time zone.
- **RT05.** A09 gates activation on app, API and schema compatibility, safety, data integrity, quality and operation, with separate Release acceptance. Low-fi evidence is simulated.

## Access and privacy

| Case · actor | Trigger and success path | Alternatives, refusals and future acceptance |
| --- | --- | --- |
| CU-001 · Establish access to the Lenver account · AT01/AT03 | At entry or return without a valid session, choose registration or sign-in. Resolve proof to a stable holder and open the authorized context. A password session with unverified email is restricted to verification. | Valid Apple proof works for a hybrid holder even with pending password verification. Relay or absent contact is allowed. Matching email routes to consent and proof under CU-004. Invalid credentials, provider cancelation, expiration, network failure and an already linked subject preserve identity and suppress prior reads. |
| CU-002 · Prove email control · AT01/AT04 | For a pending password email, request or resend the applicable challenge, present proof, bind it to the same holder and recheck access before opening financial data. | Expired, reused, invalid or other-holder links never grant access. If status is unavailable, retain the restriction and offer recovery. Apple has an independent guard. |
| CU-003 · Restore access to the same holder · AT01/AT03/AT04 | After credential or method loss, request recovery with a neutral response. Verified email can authorize a valid challenge and new password. Re-enter and recover the same holder's history and eligible intent. | Use provider recovery or another linked method for Apple, and an existing usable method after mere expiry. Invalid, expired or consumed proof and mismatched confirmation have distinct paths. Extra recovery with zero usable methods is undecided. A request for an unknown address remains neutral. B never transmits A's intent. |
| CU-004 · Manage sign-in methods · AT02/AT03/AT04 | From account security or an identity collision, prove both methods, review, consent and commit only after rechecking external identity exclusivity. Password change requires contextual proof. | Cancel leaves links intact. Unlinking at commit must leave another usable method even if two removals compete. External revocation can leave none. Refused, canceled and uncertain acts remain distinct. Query uncertain management outcomes through their own read-only path. |
| CU-005 · View the Lenver account and privacy · AT02 | Open Account from Home, find identity, access, security, privacy, support, applicable preferences and deletion, and return to the authorized originating context. Financial setup belongs to the financial account. | Use Account-first B. Expired session or privacy transition suppresses sensitive data. Test discovery, return and system app-switcher privacy in both languages. Switching to B must disclose no A figures or pending actions. |
| CU-006 · Sign out · AT02 | End local authorization, handle remote session revocation and hide data and pending actions before returning to entry. | Remote revocation can remain unconfirmed while local sign-out stands. An eligible uncertain financial intent stays protected and can resume only for the same authenticated holder, environment and target. |
| CU-007 · Delete the Lenver account and follow its outcome · AT02/AT03 | Explain effects, reauthenticate, explicitly confirm and establish protected lookup authority before transmitting. Acceptance closes admission and coordinates competing writes, identifiable and local data, sessions and applicable Apple revocation. | Cancel before confirmation creates no accepted request. An earlier committed effect enters deletion scope. An incompatible write cannot commit afterward. Distinguish rejection without effect, response loss, in progress, partial and demonstrated completion. Lookup is limited to the original even after session invalidation and cannot expose financial data or another holder's outcome. |

## Baseline, recording and history

| Case · actor | Trigger and success path | Alternatives, refusals and future acceptance |
| --- | --- | --- |
| CU-008 · Establish the financial baseline · AT02 | With authorized access and a missing baseline, declare the signed balance immediately before editable T0 in the account zone, preview and confirm. New account and baseline commit together. | Explicit zero and negative amounts are allowed. Deferral may leave no account, and authorized legacy history remains readable. Adoption requires ownership, integrity and all-writer compatibility and preserves IDs and pre-coverage history. Suspended goes to CU-018. Concurrent opening yields one active account and first baseline. S01 yields BRL 845 and the conditional S06 fixture yields BRL 827. |
| CU-009 · Resume financial tracking · AT02 | On Home, return or refresh, read current calculated balance and reference, recent transactions, open checks and uncertain submissions under authorized scope. Continue to the relevant action. | Missing baseline allows setup later. Valid prior readings show their time and stale label. Suspension suppresses affected figures, and loading, empty and expired remain distinct. After the S02 coffee, current is BRL 827 and the check remains +BRL 45. |
| CU-010 · Record a routine transaction · AT02 | Record actual income or expense for an owned account with active baseline. Expense and current instant are editable defaults. Description is optional. Confirm once and refresh history and balance after receipt. | Accept eligible day-only occurrences when coverage, cutoffs and present are unambiguous. An expense at T0 is included. Zero, fractions, excess range, wrong currency, future and pre-coverage input are rejected. Protection failure sends nothing. Uncertainty goes to CU-020. |
| CU-011 · Read history and transaction versions · AT02 | View authorized history even before a legacy baseline. Search description across the server-side collection and combine period and type. Open details, original and versions. | Preserve filters across an incompatible revision restart and drop prior pages. Show actual date-only precision, correction and invalidation. Start of first day is inclusive, start after last day exclusive. Test matches beyond the first page, stable ties and no cross-holder or mixed-revision results. |

## Checks and recovery

| Case · actor | Trigger and success path | Alternatives, refusals and future acceptance |
| --- | --- | --- |
| CU-012 · Record a balance check · AT02 | Provide reported balance and unambiguous eligible cutoff, review and confirm. Freeze observation, holder, recording time and original comparison under a new ID. | Explicit signed values including zero are allowed. Independent real observations may share a cutoff. Stale preview, suspension and pre-coverage cutoff cannot commit. S02 freezes BRL 800 reported, BRL 845 calculated and +BRL 45 original, with no new transaction. |
| CU-013 · Find and explain a discrepancy · AT02 | From Review or Home, inspect current and historical readings and compare candidates by account, date, amount and description. Deliberately link an eligible existing expense with identifiable proof, preserving balance and remaining difference. | Defer, or record a distinct eligible retroactive expense and originating link together. A numerical match requires nonzero original and proved contribution. Reuse with another eligible pair needs independent consent. Stale revision, mismatched references, ambiguity and insufficient proof return to review. A superseded or unavailable check preserves authorized originals. |
| CU-014 · Correct or restore an association · AT02 | Inspect the owned pair and its trail. Preview, state a reason and confirm removal of mistaken attribution while preserving link, evidence and transaction. | Restore the same pair only with fresh evidence, current eligibility, review and a distinct audited intent and receipt. A successor requires its own association. Replay of the original leaves withdrawal intact. No extra transaction or balance change. Conflicting or ineligible requests preserve the prior state. |
| CU-015 · Correct or invalidate a transaction · AT02 | For a recorded error, inspect original and versions, preview balance and cutoff effects, give a reason and confirm an audited change of amount, occurrence or description. | Invalidate to remove contribution while preserving history. CU-019 governs precision-only changes. Reject change of identity, type or currency, stale revisions and pre-commit failures. Economic reversal is a different fact. S03 correction gives BRL 783 and +BRL 1 with proof of reopening, S04 invalidation gives BRL 802 and +BRL 20. |
| CU-016 · Replace an incorrect balance check · AT02 | With a current owned check and eligible corrected cutoff, preview, justify and confirm a successor with new identity and original comparison. Preserve the predecessor and its links. | Correct amount, cutoff or both. Use CU-012 for a new observation. Reassociate eligible evidence deliberately under CU-013. Replay returns the existing successor. Branches, cycles and stale previews cannot commit. S03 replacement at 11:00 gives +BRL 1. A → B → C preserves both reasons at B. |

## Coverage and submission outcome

| Case · actor | Trigger and success path | Alternatives, refusals and future acceptance |
| --- | --- | --- |
| CU-017 · Suspend figures affected by an incorrect baseline · AT02 | For a verified baseline error, preview suspension, reauthenticate, give a reason and explicitly confirm at the current revision. Hide affected derived figures, retain originals and block dependent writes. | Cancel before commitment or correct under CU-018. An expired preview or insufficient authority leaves the prior state. In S10 a zero original remains zero and the current remaining difference is unavailable. |
| CU-018 · Correct the declared financial baseline · AT02 | With an authorized, current procedure and corresponding declaration, preview and reauthenticate, give a reason and confirm new terms and a coherent recalculation. Preserve prior terms and comparisons. | Change amount at the same T0 or change T0 with a corresponding declaration. If T0 crosses a relevant interval, refine under CU-019 then obtain a new preview, or defer. Pre-coverage cutoff is unavailable. No attribution arises from moving coverage. S10 gives BRL 837 current, BRL 855 at cutoff, +BRL 55 remaining and +BRL 45 original. |
| CU-019 · Refine a known occurrence · AT02 | When a cutoff or T0 requires precision, use the existing owned transaction's original interval, set an unambiguous time and offset, review impact and commit an audited refinement. | Keep day-only precision when unambiguous. Resolve repeated hours, reject nonexistent, future and out-of-interval times or stale previews. Defer if proof is insufficient. Preserve identity, type, amount, description and original. Test 23-hour and 25-hour days. |
| CU-020 · Determine and resume an uncertain submission · AT02 | On interrupted transmission, response loss or restart, show uncertainty in context and Home. Restore the protected original intent for the same authenticated holder, environment and target. Query with zero writes, or explicitly resubmit the same terms under MF03.1. Refresh authoritative reads separately. | An eligible resubmission can recover a receipt or commit once under fresh guards. An altered body with the same key conflicts. Definitive rejection without effect allows a new edited intent. Protection failure sends nothing. Local loss, expiry or another device cannot authorize a duplicate. In S07 a single coffee returns one receipt and BRL 827. |

## Acceptance examples

These examples are future product oracles. The model verification has its own [evidence](functional-traceability.md#focal-mf031-verification).

| Case | Success oracle | Refusal or alternative oracle |
| --- | --- | --- |
| CU-001 | Verified password and valid Apple both identify the same authorized hybrid holder. | Unverified password blocks reads and writes. Canceling provider access or encountering matching email alone preserves identity. |
| CU-002 | After valid proof and successful readback, A opens financial context through password. | B's link, an expired link or invalid proof leaves A restricted and reveals nothing about B. |
| CU-003 | Valid recovery preserves A's history. | Unknown email gets a neutral response. Reused proof cannot reset credentials, and B sends zero A intents. |
| CU-004 | Consent and proof of both methods link Apple and preserve the holder. | Two voluntary removals cannot remove the last usable method. External revocation may do so. |
| CU-005 | Discover privacy and deletion and return to Home in both languages. | App switcher and holder change reveal no prior figures or pending actions. Verify on device. |
| CU-006 | A returns to the same eligible original intent after sign-out. | B cannot see or submit it. Remote network failure leaves local access closed. |
| CU-007 | Coordinate applicable Apple revocation and two competing writes, then follow original status after invalidating the session. | Cancelation creates no request. Timeout or pending revocation proves no completion. B cannot lookup A's request. |
| CU-008 | Explicit zero and negative terms are valid and concurrent opening leaves a single account. | Unproved legacy remains blocked. With approved S06 fixture proof, only coffee contributes and the result is BRL 827. |
| CU-009 | Coffee leaves current BRL 827 and +BRL 45 at the cutoff. | Suspension never shows zero in place of unavailable. B cannot see prior A data or intent. |
| CU-010 | BRL 55 at T0 reduces BRL 900 to BRL 845. Income adds. | T0 minus one minute, future and above-range attempts preserve count and revision. Timeout yields uncertainty. |
| CU-011 | Find an authorized description beyond page one, using type and both date boundaries. | Revision race never mixes pages. Date-only is not presented as known midnight and B gets none of A's results. |
| CU-012 | Freeze BRL 845 / BRL 800 / +BRL 45, unchanged by later unrelated movement. | Stale preview, pre-T0 cutoff or suspension cannot confirm. |
| CU-013 | S03 distinguishes +BRL 20, attributed zero and proved reopening. | One candidate still requires confirmation. Numerical match without a nonzero original and contribution cannot authorize the existing link. |
| CU-014 | Withdrawal and later eligible restoration retain pair, transaction, origination and each decision. | Replay withdrawal or original association leaves the current pair unchanged. Incompatible evidence and stale preview create no new link. |
| CU-015 | Audited correction yields 783/+1 in S03 and invalidation yields 802/+20 in the S04 branch. | S09 loser returns to review. Pre-commit failure creates no isolated audit entry. |
| CU-016 | The 11:00 successor has its own +BRL 1 and ID. An intermediate check exposes two reasons. | Replay returns the same successor and stale competing requests cannot branch. |
| CU-017 | Suspend projected balance and recalculated figure while preserving the original. | An original zero stays zero while remaining difference is unavailable. Dependent writes remain blocked. |
| CU-018 | Same-T0 correction yields 837 current, 855 at cutoff, +55 remaining and +45 original. | Ambiguous new T0 prevents confirmation. Coverage reclassification creates no attribution. |
| CU-019 | Refine within the known day with the same identity and obtain fresh cutoff preview. | Refuse amount changes, invalid or out-of-interval time. Exercise 23/25-hour days, repeated and nonexistent times. |
| CU-020 | S07 gives one effect and receipt before or after lost response. S09 returns winner despite stale preview. | Protection failure sends zero requests. B cannot see or send A's intent. Conflicting body cannot replace the original. |

## UML views

The rectangle is the system boundary. Ellipses are objectives and solid links show actor participation. Conditional paths live in the catalog. In the SVGs each actor, case and association remains independently editable.

| View | Editable diagram and GitHub preview |
| --- | --- |
| Identity and access | [SVG](diagrams/01-access.svg) |
| Lenver account and privacy | [SVG](diagrams/02-account.svg) |
| Baseline, transactions and history | [SVG](diagrams/03-finances.svg) |
| Balance checks and recovery | [SVG](diagrams/04-checks.svg) |
| Coverage and submission recovery | [SVG](diagrams/05-continuity.svg) |

[Approved model](functional-model.md) · [Behavioral model](behavior.md) · [Traceability](functional-traceability.md)
