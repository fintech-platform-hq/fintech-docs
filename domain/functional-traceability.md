# Functional model traceability and evidence

## Authority and provenance

| Approved source | Scope | Archive SHA-256 |
| --- | --- | --- |
| MF01.1 closure, 2026-11-24 | MD-004/006. Domain, glossary, I01–I13, A01–A12 and S01–S11. Checklist attachment revision 2. | `b4bcfdc3ad5ae43914319ff3ee338a30c0f762530e8325b43688e9bd5ed45dcc` |
| MF02 closure, 2026-11-24 | MD-007/008. Twenty use cases and five UML views. Checklist attachment revision 1. | `796a670c7b2e3c22b251f5a35a665c628e058f2a4c5e4cd35145dbfa74645dbb` |
| MF03.1 v1.0.0, 2026-11-25 | MD-005/012. Fourteen state machines, sixteen activities, focal revision and three decisions. Checklist attachment revision 1. | `badbe2101976376a00558113e619e0642bc2228702233f7edc5fbfa05de029ed` |

The Confluence requirements baseline v0.2 (page 4391010, revision 16) and C+A proposal (5865474, revision 6) identify F, Q and R references. The product decisions (98326) carry UX01, A01–A12 and the MF03.1 decisions. Approval and evidence inventory are in the Master Checklist (9699329). Approved MF03.1 refinements govern the affected observable paths in earlier models. The English pages adapt the approved material for public reading, retaining functional identifiers and using synthetic examples. Private evidence and source archives are held with the approval record.

Cross-reference [the domain and its invariants](functional-model.md), [the use-case catalog](use-cases.md), [the scenario and decision fixtures](functional-scenarios.md), [behavior and guards](behavior.md), [state transitions](state-transitions.md) and [activity flows](internal-activities.md).

## Case traceability

Requirements, invariants, decisions, UX01 references and fixtures retain the approved MF02 links. State and activity columns trace the MF03.1 graphs. A09/CA09 remain distribution gates. A11/CA11 govern terminology and presentation.

| Case | States | Activities | Requirements | Invariants and decisions | UX01 | Fixtures |
| --- | --- | --- | --- | --- | --- | --- |
| CU-001 | ME01 ME02 ME03 ME04 ME05 | AP01 | F01 Q04 · R1 R8 | I01 I02 I13 · A02 A09 A11 | UX01-01 UX01-02 UX01-03 UX01-05 UX01-06 UX01-16 UX01-17 UX01-18 | S08 CA02 CA09 CA11 |
| CU-002 | ME03 ME04 ME05 | AP02 | F01 Q03 Q04 · R1 | I01 I02 I13 · A02 A11 | UX01-02 UX01-03 UX01-14 UX01-16 UX01-17 UX01-18 | S08 CA02 CA11 |
| CU-003 | ME01 ME02 ME03 ME04 ME05 | AP02 | F01 Q02 Q03 Q04 · R1 R8 | I01 I02 I13 · A02 A08 | UX01-02 UX01-03 UX01-05 UX01-14 UX01-15 UX01-16 | S08 CA02 CA08 |
| CU-004 | ME01 ME02 ME03 ME04 ME05 ME14 | AP03 AP04 AP15 | F01 Q03 Q04 · R1 R8 | I01 I02 I11 I13 · A02 A11 | UX01-02 UX01-03 UX01-04 UX01-05 UX01-14 UX01-15 UX01-16 | S08 CA02 CA11 |
| CU-005 | ME01 ME05 ME06 ME13 | AP01 AP04 AP13 AP16 | F01 Q03 Q04 · R1 R8 | I01 I02 I13 · A02 A09 A11 | UX01-04 UX01-08 UX01-08.1 UX01-15 UX01-17 UX01-18 | S08 CA02 CA09 CA11 |
| CU-006 | ME05 ME11 ME12 ME13 | AP04 AP12 | F01 Q02 Q03 Q04 · R7 R8 | I01 I02 I12 I13 · A02 A08 | UX01-04 UX01-14 UX01-15 | S08 CA02 CA08 |
| CU-007 | ME01 ME02 ME05 ME06 ME11 ME12 | AP05 AP06 AP12 | F01 Q03 Q04 · R8 | I01 I02 I03 I13 · A01 A09 | UX01-04 UX01-14 UX01-15 UX01-17 | S08 CA01 CA09 |
| CU-008 | ME07 ME11 | AP07 AP12 | F01 F02 F04 Q01 Q02 Q03 Q04 · R1 R2 R7 R8 | I01 I04 I05 I06 I11 I12 I13 · A03 A06 A07 A08 | UX01-07 UX01-07.1 UX01-09 UX01-14 UX01-18 | S01 S06 S07 S08 S09 CA03 CA06 CA07 CA08 |
| CU-009 | ME07 ME12 ME13 | AP07 AP11 AP13 | F02 F04 F05 Q01 Q02 Q04 · R1 R2 R3 R4 R8 | I01 I04 I05 I07 I08 I10 I11 I13 · A03 A05 A08 A11 | UX01-08 UX01-08.1 UX01-09 UX01-12 UX01-14 UX01-17 UX01-18 | S01 S02 S06 S08 S10 CA03 CA05 CA08 CA11 |
| CU-010 | ME07 ME08 ME11 ME12 | AP08 AP12 | F01 F02 F03 F04 Q01 Q02 Q03 Q04 · R2 R4 R7 R8 | I01 I04 I05 I06 I08 I11 I12 I13 · A06 A07 A08 A11 | UX01-08 UX01-09 UX01-10 UX01-10.1 UX01-14 UX01-16 UX01-17 UX01-18 | S01 S02 S07 S08 S09 CA06 CA07 CA08 CA11 |
| CU-011 | ME08 ME13 | AP07 AP08 AP13 | F01 F04 F07 Q01 Q03 Q04 · R2 R7 R8 | I01 I04 I05 I06 I11 I13 · A03 A10 A11 | UX01-08 UX01-08.1 UX01-10.1 UX01-11 UX01-11.1 UX01-13 UX01-17 UX01-18 | S01 S06 S09 S11 CA03 CA06 CA10 CA11 |
| CU-012 | ME07 ME09 ME11 ME12 | AP09 AP12 | F02 F05 Q01 Q02 Q03 Q04 · R3 R4 R7 R8 | I01 I04 I05 I06 I07 I08 I10 I11 I12 I13 · A05 A06 A07 A08 A11 A12 | UX01-08 UX01-08.1 UX01-09 UX01-12 UX01-14 UX01-17 UX01-18 | S01 S02 S03 S07 S09 S10 CA05 CA06 CA07 CA08 CA11 CA12 |
| CU-013 | ME08 ME09 ME10 ME11 ME13 | AP09 AP10 AP12 AP13 AP14 | F04 F05 F06 Q01 Q02 Q03 Q04 · R2 R3 R4 R5 R7 R8 | I01 I04 I05 I06 I07 I08 I09 I10 I11 I12 I13 · A04 A05 A06 A07 A08 A11 A12 | UX01-07.1 UX01-12 UX01-13 UX01-14 UX01-17 UX01-18 | S02 S03 S04 S05 S09 S10 S11 CA04 CA05 CA06 CA07 CA08 CA11 CA12 |
| CU-014 | ME08 ME09 ME10 ME11 | AP10 AP12 AP14 | F05 F06 F07 Q01 Q02 Q03 Q04 · R4 R5 R7 R8 | I01 I08 I09 I10 I11 I12 I13 · A04 A05 A08 A12 | UX01-12 UX01-13 UX01-14 | S03 S05 S09 CA04 CA05 CA08 CA12 |
| CU-015 | ME08 ME09 ME10 ME11 | AP08 AP12 | F04 F05 F07 Q01 Q02 Q03 Q04 · R2 R4 R6 R7 R8 | I01 I04 I05 I06 I08 I09 I10 I11 I12 I13 · A05 A06 A07 A08 A11 | UX01-13 UX01-14 UX01-17 UX01-18 | S03 S04 S07 S09 S11 CA05 CA06 CA07 CA08 CA11 |
| CU-016 | ME09 ME10 ME11 | AP09 AP10 AP12 AP14 | F05 F06 F07 Q01 Q02 Q03 Q04 · R3 R4 R5 R7 R8 | I01 I06 I07 I08 I09 I10 I11 I12 I13 · A04 A05 A06 A08 A11 A12 | UX01-12 UX01-13 UX01-14 UX01-17 UX01-18 | S03 S09 CA04 CA05 CA06 CA08 CA11 CA12 |
| CU-017 | ME07 ME09 ME11 ME13 | AP11 AP12 AP13 | F02 F04 F05 F07 Q01 Q02 Q03 Q04 · R1 R3 R6 R7 R8 | I01 I04 I05 I07 I08 I10 I11 I12 I13 · A05 A06 A07 A08 | UX01-09 UX01-13 UX01-14 UX01-17 | S09 S10 CA05 CA06 CA07 CA08 |
| CU-018 | ME07 ME09 ME10 ME11 ME13 | AP11 AP12 AP13 | F02 F04 F05 F07 Q01 Q02 Q03 Q04 · R1 R2 R3 R4 R6 R7 R8 | I01 I04 I05 I06 I07 I08 I10 I11 I12 I13 · A05 A06 A07 A08 A09 | UX01-01 UX01-07 UX01-13 UX01-14 UX01-17 UX01-18 | S09 S10 S11 CA05 CA06 CA07 CA08 CA09 |
| CU-019 | ME07 ME08 ME09 ME11 | AP08 AP09 AP11 AP12 | F03 F05 F06 F07 Q01 Q02 Q03 Q04 · R1 R2 R4 R6 R7 R8 | I01 I05 I06 I07 I08 I10 I11 I12 I13 · A06 A08 A11 | UX01-07 UX01-10 UX01-12 UX01-13 UX01-14 UX01-18 | S09 S10 S11 CA06 CA08 CA11 |
| CU-020 | ME05 ME06 ME08 ME10 ME11 ME12 ME13 | AP04 AP05 AP07 AP08 AP09 AP10 AP11 AP12 AP13 AP14 | F01 F02 F03 F05 F06 F07 Q01 Q02 Q03 Q04 · R5 R7 R8 | I01 I02 I03 I11 I12 I13 · A01 A02 A08 A09 | UX01-01 UX01-14 UX01-15 UX01-17 | S06 S07 S08 S09 CA01 CA02 CA08 CA09 |

## Focal MF03.1 verification

The closure package verified structure and reachability, exercised desk scenarios and ran numerical and graph oracles. The counterexamples below describe mistakes that the focal verification detected in deliberately changed fixtures. These are model results.

| Item | Trace | Expected and checked model outcome |
| --- | --- | --- |
| B1 · Separate lookup and write | AP12 a/q/c, p/s/j/h/b/e/g/w/o · ME11 · G01/G02/G05 · CU-020 · S07/S09, CR03/05 | Lookup cannot reach write. Inconclusive lookup is indeterminate. Authorized original precedes stale revision and barrier for disclosure. Conflicting body preserves original. |
| B2 · Knowledge, loss and scope | AP12 m/p/l/f/u/j · ME12 · G01/G05 · CU-006/010/020 · S08, CA08 | No-network proof permits protection repair. Restart keeps intent. Loss after possible send blocks equivalent work. Holder, environment and target isolate original. |
| B3 · Late response | AP13 g/j/l/d/r · ME13 · G07 · CU-005/009/011 · S08, CA10, CR06 | A → B, another session of A, another account, filter, revision or known suspension can suppress exposure. Confirmed empty needs a valid response. |
| B4 · Original deletion | AP05 v/p/e/g/b/r/o/u · AP06 v/d/c/u/r · ME01/ME06 · G01/G02/G05 · CU-007 · CA01, CR02 | Protect authority first and distinguish cancelation, rejection and loss. Consult only the original after invalidation. Exercise both orders around the write barrier. |
| B5 · Civil time and refinement | AP08 s/p/a/o · AP09 v/q/p · AP11 v/q/p · ME07/08/09 · G03/G09 · S10/S11, CA06, CR07 | T0 at civil start includes the entire day. Cutoff at start is ambiguous. Preserve exclusive day end and DST. Refinement preserves identity and suspension. Require new preview or defer. |
| B6 · Attribution and pair | AP10 v/t/e/d/a/o · AP14 t/v/g · AP12 b/e · ME09/10/11 · G04–G06/G09 · S03–S05, CA04/05/12 | Current eligibility, attributable contribution at the cutoff, effect-specific receipts and most recent applicable episode. Explicit reuse preserves the genuine expense identity. |
| O01 · Method management | CU-004 · AP03/AP04/AP15 · ME14 | With contextual proof, authorized password change succeeds. Cancelation preserves method and password. After response loss, A's lookup reads the original; B cannot read it. |
| O02 · Privacy and return | CU-005 · AP16/AP13 · ME05/13 · Account-first B | A opens from Home and revalidates before restoring origin after transition. Background, app switcher, expired session or B suppress data and intents. |
| Required scenarios | S03 11:00 successor, S06 adoption and lost reply, S07 fourteen targets, S08 two restarts and expiry | Preserve the independent 08:00 check, retain IDs and pre-coverage history in S06, converge to one logical effect/receipt per target, maintain scoped intent and disclosure rules. |

| Race or failure | Scenario |
| --- | --- |
| CR01 · Last usable method | AP04 rechecks concurrent removals at commitment. One removal may win; external revocation can still remove the last method. |
| CR02 · Deletion and writes | AP05/AP12 include earlier committed effects in scope and reject later incompatible commits. Protected lookup never reopens financial authority. |
| CR03 · Replay and reference | AP10/AP12 return the authorized original despite newer revision, preserve it on conflict and permit receipt recovery by compatible genuine identity. |
| CR04 · Competing corrections | AP08/AP09/AP14 send the losing same-revision correction, attribution change or successor to review at the new revision. |
| CR05 · Lost response and failed refresh | AP12/AP13 prevent partial pre-commit effects and preserve committed effect, trail, revision and receipt even when refresh fails. |
| CR06 · Late read | AP13 exercises A → B, A → sign-out → new session of A, account/filter/revision changes and known suspension before publication. |
| CR07 · Refinement versus new T0 | AP08/AP11 revalidate on a new revision. T0 reclassification alone confers no credit. |
| CR08 · External subject exclusivity | AP01/AP03 allow one valid Apple association across competing holders and refuse the other without disclosure. |

## Implementation and operational validation

The [historical domain description](domain-definition.md) records the early implementation snapshot. See [I1-A](../architecture/i1-a-delivery.md), [I1-B](../architecture/i1-b-delivery.md), [I2](../architecture/i2-delivery.md), [I3](../architecture/i3-delivery.md), [I4](../architecture/i4-delivery.md), [I5](../architecture/i5-delivery.md) and [authentication](../architecture/authentication.md) for implementation evidence. The I1–I5 financial journey is integrated with an iOS Debug opt-in. The current published API is documented in the [backend OpenAPI contract](https://github.com/lenver-hq/lenver-backend/blob/main/openapi.yaml).

| Area | Historical I5 evidence | Remaining contract, product or operational evidence |
| --- | --- | --- |
| CU-001–006 · LAC-01 | Password and Apple authentication, Apple linking from a password session and session revocation. | Full email verification/recovery and read/write gates, method management in both directions, concurrent last-method proof, Apple relinking, affected session policy and zero-method recovery decision. An additional recovery channel would need ownership proof, anti-fraud controls and neutrality. Integrated privacy, sign-out and app-switcher behavior need device proof. |
| CU-007 · LAC-02 | Sign-out and session revocation. | Demonstrate coordinated deletion, barrier, provider revocation, discard and protected lookup. Define scope, window, privacy, temporary retention, purge and proof of disposal. |
| CU-008–010 · LAC-03 | BRL opening, protected writes and authoritative calculations. I5 returns `LEGACY_ADOPTION_BLOCKED`. | Inventory real eligibility, prove ownership and integrity and check all writers before enabling adoption. Test fallback reads, opening and input presentation in the journey. |
| CU-011 · LAC-07 | Cursor, occurrence ordering and restart after stale revision. | Full authorized server-side search and composed period/type filters, normalization, stable tie-break and coherent navigation through changing data. |
| CU-012–016 · LAC-04/05/09 | Dated checks, recovery evidence and optional reference, pair constraints, correction/invalidation, replacement. I5 projects one `replacementReason` at an intermediate check. | Explicit zero-match contribution guard, link withdrawal/restoration and reuse contracts, independent reason for each edge, and proof of the preceding episode in the same check. Reconstruct from trustworthy revisions or record complete durable proof. Validate audit and correction authority per act. |
| CU-017–019 · LAC-06/09 | Baseline suspension, correction, civil-day precision and refinement. | Contract precision, DST and presentation, full-range exact transport, and contextual reauthentication for major corrections. Validate interaction of suspension, occurrence refinement and fresh preview. |
| CU-020 · LAC-08 | Protected body and key, replay and refresh paths in the I5 reference. | Authorized pure lookup, recoverability window, protected scope, local loss, purge, equivalent-operation guard and end-to-end proof on service and physical iPhone, including lock, sign-out and every target. |
| A09 · LAC-10 | Integrated Debug opt-in with historical implementation and acceptance evidence scoped to I1–I5. | Verify real app/API/schema and consumer compatibility, legacy data, security, privacy, operational readiness, Apple/email and device behavior. Define activation, rollback and flag removal. Release has its own acceptance. |

The next product-design stage is the integrated low-fi batch 05. Its native behavior, accessibility and localized journey remain subject to their own validation.
