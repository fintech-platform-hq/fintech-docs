# MF03.1 behavioral model

The owner accepted v1.0.0 on 2026-11-25 for MD-005 and MD-012. The model defines observable outcomes and authorities. Machine states and activities do not prescribe stored phases, endpoints, database types, queues or distributed transactions.

- [State machines and transition tables](state-transitions.md)
- [Internal activities and alternative flows](internal-activities.md)
- [Case, decision and verification traceability](functional-traceability.md)
- [MF01.1 domain and invariants](functional-model.md)

## Dimensions

ME01–ME10 model persistent lifecycles. ME11 models authoritative submission outcomes. Its undecided phase can exist without a stored pending record. ME12 models what the client knows and ME14 models separate knowledge of sign-in management. ME13 controls when a reading may be shown. A committed effect can coexist with uncertainty. Local sign-out can coexist with unconfirmed remote revocation. Availability, attribution, method usability, preview expiry and return of discrepancy are derived from evidence, scope and revision. Loading, stale, empty, unavailable and uncertain are presentation conditions and may coexist where applicable. Versions, receipts and replacement links preserve immutable evidence. Initial circles identify the start of an instance, and final circles end the represented behavior. Refusal self-loops preserve state. Unlabeled activity diamonds converge branches.

## Common guards

| ID | Required result |
| --- | --- |
| G01 · Scope | Authorize holder, account and operation for reads, writes, linking and reviews. Password financial reads and writes require verified email. Valid Apple proof gates its own path for the same holder. Linking requires consent, both proofs and exclusivity. A matching address, relay or identifier alone is insufficient. |
| G02 · Commitment | Revalidate current revision, owner, target, authority and deletion barrier at commit. An earlier preview provides no continuing authority. Preserve original, author, time, revision, target, applicable reason and receipt. A losing incompatible decision reviews the current state. |
| G03 · Money and time | Exact BRL, 1–999,999,999 cents per transaction, direction by type, independent signed Int64 baseline and reported amounts, exact aggregate beyond that range. T0 and cutoff inclusive. Day-only requires unambiguous predicates in the account zone. Ask for precision or defer. Disambiguate repeated times and reject nonexistent times rather than shifting them. |
| G04 · Attribution | At a current calculable open discrepancy, link with human confirmation and identifiable evidence of the fact. A numerical match requires a nonzero original and proved attributable contribution. One candidate still requires consent. Genuine account/source/ID identifies one fact. Compatible identity can recover its receipt. Reuse in another eligible pair takes its own decision and does not duplicate the expense. Conflicting reference returns to review. |
| G05 · Original first | Verify scope, privacy, intent and effect or pair. Authorized canonical result precedes fresh revision and write guards. Lack of receipt, local loss and network failure prove no rejection. The deletion barrier blocks new commitment. Original deletion proof authorizes only that request's result. |
| G06 · Active queue and history | Superseded check leaves the queue, retaining original and links. Independent observations can share cutoff. A successor has a new identity, later creation revision and at most one immediate edge each way. Preserve both reasons at an intermediate check. |
| G07 · Read privacy | Bind the response to session, holder, account, authorized filters, temporal origin and compatible revision. Search the full authorized set and combine type and civil-day period. Discard incompatible pages and keep authorized filters on restart. Session end, holder change or known invalidation suppresses affected data, including from late responses. |
| G08 · Distribution | A09 and CA09 gate app/API/schema and consumer compatibility, nullable and unavailable results, data, security, quality, operation, activation and rollback. Debug opt-in or a generic build cannot satisfy it. |
| G09 · Account-holder corrections | Only the authorized holder can commit. Require reason, current expected revision, explicit confirmation and audit. Contextual reauthentication applies to higher-impact acts, including baseline suspension or change. A purchase receipt or external ID is an optional evidence class. A holder declaration proves the decision. Fact identity and contribution remain subject to G04. |

## The three MF03.1 decisions

| Decision | Approved behavior | Follow-up evidence |
| --- | --- | --- |
| LAC-02 · Deletion outcome | Establish protected authority before transmission. Query only the original request even after session invalidation. Distinguish rejection, in progress, partial and demonstrated completion. Keep uncertainty where no result can be proved. Authority yields zero access to the account and financial data. Retention is minimal, temporary and justified. | Security, privacy and contracts define proof, lifetime, purge, coordination and proof of disposal. |
| LAC-04/09 · Authority and evidence | G09 governs authorized holder, contextual reauthentication for higher impact, reason, expected revision, consent and audit. Purchase receipt and external ID are optional, and declaration establishes the decision. G04 still governs proof of fact, identity and contribution at cutoff. | Specify evidence by act. Demonstrate the trail and both reasons on an intermediate balance check. |
| LAC-08 · Submission recovery | Retain protected intent until a definitive outcome. Pure lookup only reads. Explicit resubmission precisely preserves key, body and scope. Outcome remains recoverable while the service admits resubmission. Expiry and local loss preserve duplicate protection. Demonstrably independent work may proceed under concurrency and consistency guards, within the constraint against a multiple-write queue. | Define service and local retention windows, scope, evidence, expiration, purge and loss recovery. Verify on service and device. |

## Calculations and presentation

Compute the [domain projection](functional-model.md#invariants) for an authorized revision and eligible cutoff. A missing/suspended baseline or a cutoff before T0 makes derived amounts unavailable. Authorized history and original comparisons remain available under their own guards. Only an eligible missing baseline can be opened. Available zero is a figure with a distinct meaning.

| Result | Classification |
| --- | --- |
| Remaining difference = 0, original ≠ 0 and proved contribution = −original | Attributed resolution. Exclude unchanged previously counted expenses and isolated T0 reclassification. |
| Other zero | Numerical match. |
| Nonzero with original sign, lower magnitude and evidence of reduction | Partial explanation. |
| Other nonzero, including reversal | Open discrepancy. |
| Nonzero returns with proved applicable attributed zero on the same check | Reopened discrepancy. |
| Nonzero returns following a proved numerical match on that check | Difference after a numerical match. |
| Applicable zero episode lacks proof | Present current classification. |

Find the zero episode immediately preceding the return in the proved trajectory of the same check. A more recent zero supersedes an earlier candidate. An intervening open period ends that zero episode. Only cross unavailable intervals with proved continuity. Retain the indication during the current open episode while continuity is proved. A successor starts its own trajectory. Keep superseded status independent of availability or classification.

Publish confirmed empty only after a valid empty response. Loading, error, expired authorization and unavailable cutoff are separate. Prior reading requires authorized origin, time and ongoing validity and is labeled stale during refresh when applicable. Suppress known invalidated figures. An uncertain write can be visible on Home and its original context while authorized history remains readable. Refresh independent of authoritative write result.

## Commitment and recovery

AP07–AP11 and AP14 are callers of one AP12 commitment. Each supplies its specific guards for revalidation at the authoritative decision. New account and first baseline, distinct recovery expense and originating link, and all audited effects with their trail, revision and receipt commit as units. Failure before commitment leaves every element unchanged. Projection or refresh may occur separately. AP05/AP06 cover deletion and AP15 covers method-management outcomes.

| Mode | Precedence and result |
| --- | --- |
| Pure lookup | Prove result authority and original effect/pair identity. Return demonstrable canonical outcome or indeterminate. Zero writes even with write authority and a missing receipt. |
| First submission | Protect before network, consent and validate scope and body. Resolve an authorized existing result after checking for identity/body conflicts. Only then evaluate fresh-execution guards for an absent result. |
| Explicit resubmission | Same key, body, holder, environment, target and effect, with eligible proof. Different body or reference conflicts. Authorized original result precedes stale preview and write barrier. Without a canonical outcome, apply fresh admission, revision and per-action guards. Concurrent resubmissions converge. |

Proved no-network protection failure permits repair before sending. Restart restores an intact intent. After possible transmission, a lost response, local loss or expired proof leaves uncertainty until demonstrated outcome, and blocks an equivalent retry without the original. A definitive no-effect rejection permits new preparation. Other operations may proceed only if proved independent of both equivalence and the missing result and eligible under scope, revision, barrier and concurrency. Prepare, reconfirm, cancel or defer before commitment without changing the original. Deletion cancelation before confirmation and challenge expiry leave account state intact. A previously committed write is in deletion scope. No incompatible commitment may follow acceptance.

## Civil-time and attribution details

For a day-only occurrence in the account zone, evaluate all relevant predicates independently over [civil start, next civil start), even if their conjunction is false. The predicate occurrence ≥ T0 is uniformly true when T0 ≤ civil start and false when T0 ≥ next civil start. For occurrence ≤ cutoff, it is uniformly true when cutoff ≥ next civil start and false when cutoff < civil start. Cutoff exactly at civil start is ambiguous. Apply the same inclusive rule to the present where applicable. Resolve offsets before comparing absolute instants. Preserve 23-hour and 25-hour days.

When a new T0 or cutoff straddles a relevant known interval, seek authorized refinement or defer. A successful refinement keeps the same transaction and all other terms and requires a preview at the resulting revision. During suspension, authorized refinement of an existing occurrence can proceed without changing baseline terms or suspension. Dependent creation/checks remain blocked and figures return only in eligible revisions and cutoffs.

An existing effective expense can be linked in an open calculable discrepancy. If already counted in the original, its attribution can be zero. Numerical match requires nonzero original and proved contribution at cutoff, including a late entry of an earlier occurrence. A distinct new expense requires an open discrepancy and an identifiable eligible fact. Binding the pair has its own identity, evidence and receipt distinct from the fact. A repeat of the original association after withdrawal returns the original receipt without restoring it. Restoration has its own audited decision and receipt. Reuse of an expense in another eligible check requires an independently confirmed pair. Preserve separate identities for genuinely distinct purchases.
