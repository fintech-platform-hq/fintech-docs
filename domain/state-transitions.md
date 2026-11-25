# State machines and transitions

These MF03.1 v1.0.0 views preserve the approved machine, node and edge IDs. The columns give the event and its guard, effect or refusal. See [common guards and interpretation](behavior.md).

## ME01 · Identity and admission

[Editable UML diagram](diagrams/ME01.svg) · Persistent · CU-001 CU-003 CU-004 CU-005 CU-007 · A01 A02 A11 · I01 I02 I03 · LAC-01 LAC-02

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME01-01 | i Initial → a Identity established | Establish identity | Registration proof or valid Apple proof with an available external identity fixes the holder. |
| ME01-02 | a Identity established → a Identity established | Recognize access or recovery | Valid proof grants appropriate authorization for this holder. Matching email alone creates no link. |
| ME01-03 | a Identity established → b Closure accepted | Accept deletion | Reauthentication, explicit consent, same account and pre-established original lookup authority. Close admission and coordinate writes at acceptance. |
| ME01-04 | b Closure accepted → f Identity closed | Complete deletion | Resolve all applicable pending work and justify any exceptional retention. Retain only the protected lookup outcome and authorized retention. |
| ME01-05 | b Closure accepted → b Closure accepted | Request access or write after admission closes | Refuse access or new financial effect. ME06 governs restricted lookup. |

## ME02 · Sign-in method linkage

[Editable UML diagram](diagrams/ME02.svg) · Persistent · CU-001 CU-003 CU-004 CU-007 · A01 A02 · I01 I02 I03 · LAC-01 LAC-02

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME02-01 | i Initial → a Linked | Establish method | Eligible registration or consent, both proofs and exclusivity establish the method for the same holder. ME03 gates password financial access. |
| ME02-02 | a Linked → b Externally revoked | Recognize external revocation | Valid provider evidence removes usability. Zero usable methods can remain. |
| ME02-03 | a Linked → f Unlinked | Unlink active method | Check another usable method at commitment, then end the link and handle affected sessions. A future link starts its own lifecycle. |
| ME02-04 | b Externally revoked → f Unlinked | Unlink revoked method | Apply the same remaining-method guard. Remove the revoked link and retain authorship and exclusivity. |
| ME02-05 | a Linked → a Linked | Refuse unlinking active method | Insufficient proof or no other usable method preserves links. Return current authorized state. |
| ME02-06 | b Externally revoked → b Externally revoked | Refuse unlinking revoked method | Inadequate proof or no other usable method grants no new authority. Recover only with existing eligible proof. |

## ME03 · Password email verification

[Editable UML diagram](diagrams/ME03.svg) · Persistent · CU-001 CU-002 CU-003 CU-004 · A02 · I01 I02 · LAC-01

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME03-01 | i Initial → p Verification pending | Establish password method | Keep financial reads and writes restricted through password pending verification. |
| ME03-02 | p Verification pending → v Control verified | Prove contact control | Consume valid proof for this holder and scope and recheck financial authorization. |
| ME03-03 | p Verification pending → p Verification pending | Reject proof or fail to read status | Invalid, consumed, expired, foreign or unconfirmed proof preserves the password restriction. Apple has its own proof. |
| ME03-04 | v Control verified → v Control verified | Repeat proof | Already used proof produces no new effect and grants no authority to another identity. |

## ME04 · Verification or recovery proof

[Editable UML diagram](diagrams/ME04.svg) · Persistent per challenge · CU-001 CU-002 CU-003 CU-004 · A02 · I01 I02 · LAC-01

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME04-01 | i Initial → p Proof pending | Request proof | Limit proof to eligible holder, method and purpose. Recovery response is neutral. |
| ME04-02 | p Proof pending → c Consumed | Present proof | Valid, unexpired, unused, holder- and purpose-bound proof commits with the action. If recovering, validate new password and return to sign-in for the same holder. |
| ME04-03 | p Proof pending → e Expired | Expire proof | No verification or recovery effect. |
| ME04-04 | p Proof pending → p Proof pending | Reject presentation | Invalid, used, mismatched or unauthorized proof leaves pending state. |
| ME04-05 | c Consumed → f Ended | End consumed lifecycle | Refuse reuse. Retention and affected session policy belong to contract design. |
| ME04-06 | e Expired → f Ended | End expired lifecycle | Preserve expiry and no effect. Reissued proof starts a separate lifecycle. |

## ME05 · Lenver session and authority

[Editable UML diagram](diagrams/ME05.svg) · Persistent per session · CU-001 CU-002 CU-003 CU-004 CU-005 CU-006 CU-007 CU-020 · A01 A02 A08 · I01 I02 I03 I13 · LAC-01 LAC-02 LAC-08

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME05-01 | i Initial → a Authorized session | Complete authentication | Resolve holder, proof and account admission. Grant the scoped session and consume one-use Apple proof where applicable. |
| ME05-02 | a Authorized session → a Authorized session | Accept refresh | Valid renewable authority retains the holder and scope. Password financial verification remains a guard. |
| ME05-03 | a Authorized session → r Revoked | Confirm revocation | End remote authority on confirmed revocation. Local sign-out hides data and does not cancel an uncertain write. |
| ME05-04 | a Authorized session → e Expired | Expire session | Block use and require fresh access proof. |
| ME05-05 | e Expired → f Ended | End expired session | Keep the expired session unauthorized. Authorized re-entry creates a new session. |
| ME05-06 | r Revoked → f Ended | End revoked session | Retain revocation. Re-entry requires fresh authentication and open admission. |

## ME06 · Original deletion request

[Editable UML diagram](diagrams/ME06.svg) · Authority and outcome per request · CU-005 CU-007 CU-020 · A01 A08 · I01 I03 I13 · LAC-02 LAC-08

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME06-01 | i Initial → h Reviewing | Choose deletion | Review consequences in the authorized account. |
| ME06-02 | h Reviewing → p Protected request prepared | Prepare protected request | Establish pre-network lookup authority bound to original, holder and environment. No account access is granted. |
| ME06-03 | h Reviewing → f Ended before submission | Cancel or fail preparation | No accepted request. Distinguish cancel from failed protection. |
| ME06-04 | p Protected request prepared → f Ended before submission | Cancel protected preparation | No network and no accepted deletion. |
| ME06-05 | p Protected request prepared → e Submitted without proved result | Submit protected original | Require reauthentication, explicit consent and original lookup authority. Submit only the original and keep uncertainty on reply loss. |
| ME06-06 | e Submitted without proved result → a In progress | Accept | Validate holder and admission. Accept and close admission together with ME01. Include earlier commits and deny later incompatible effects. |
| ME06-07 | e Submitted without proved result → r Rejected without effect | Reject | Demonstrate no effect of original and preserve existing admission. |
| ME06-08 | a In progress → d Partial | Record pending work | Revocation or disposal remains unresolved. Expose partial outcome via protected proof. |
| ME06-09 | d Partial → a In progress | Resume work | Coordinate outstanding effects without repeating completed effects or reopening admission. |
| ME06-10 | a In progress → c Completed | Complete in-progress handling | All applicable effects are demonstrated and retention justified. Record minimum completion for the original. |
| ME06-11 | d Partial → c Completed | Resolve last partial item | Complete after proving all applicable effects and justifying retention. Preserve original scope. |
| ME06-12 | e Submitted without proved result → e Submitted without proved result | Lookup before a result is available | Validate original proof. Preserve uncertainty and perform no new request. |
| ME06-13 | a In progress → a In progress | Lookup in progress | Reveal only the minimal authorized original status. |
| ME06-14 | d Partial → d Partial | Lookup partial | Reveal the original partial state, no account information. |
| ME06-15 | r Rejected without effect → r Rejected without effect | Lookup rejected | Reveal definitive original rejection under proof. |
| ME06-16 | c Completed → c Completed | Lookup completed | Reveal demonstrated completion with no account access. |

## ME07 · Financial baseline and versions

[Editable UML diagram](diagrams/ME07.svg) · Persistent per account · CU-008 CU-009 CU-010 CU-012 CU-017 CU-018 CU-019 · A03 A06 A07 A08 · I01 I04 I05 I06 I07 I11 I12 · LAC-03 LAC-06 LAC-09

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME07-01 | i Initial → n Missing baseline | Assess financial context | Verify authorized new or legacy context without a baseline. A new account may not exist yet. Preserve readable legacy history. |
| ME07-02 | n Missing baseline → a Active terms | Establish baseline | Eligible preview and commitment. New account and terms commit together or authorized adoption preserves IDs and pre-coverage history. |
| ME07-03 | n Missing baseline → n Missing baseline | Reject or defer opening | Preserve prior history and unavailable amounts. Legacy requires ownership, integrity and compatible writers. |
| ME07-04 | a Active terms → s Suspended terms | Suspend | G09 requires reason and reauthentication. Retain terms and originals, suppress calculations and dependent writes. |
| ME07-05 | a Active terms → a Active terms | Correct active terms | G09, corresponding declaration and unambiguous fresh preview produce audited new version. |
| ME07-06 | s Suspended terms → a Active terms | Restore from suspension | G09 and resolved boundaries allow new terms and only eligible figures. |
| ME07-07 | s Suspended terms → s Suspended terms | Refine existing occurrence | G03/G09 allow precision-only change. Keep suspension and replace dependent preview. |
| ME07-08 | s Suspended terms → s Suspended terms | Refuse new opening while suspended | Use correction rather than a second baseline. |

## ME08 · Transaction and versions

[Editable UML diagram](diagrams/ME08.svg) · Persistent per transaction · CU-010 CU-011 CU-013 CU-014 CU-015 CU-019 CU-020 · A04 A06 A07 A08 A10 · I01 I04 I06 I07 I09 I11 I12 · LAC-04 LAC-06 LAC-08 LAC-09

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME08-01 | i Initial → v Valid transaction | Confirm transaction | G01–G03 and applicable recovery proof. Preserve original and commit originating link together when applicable. |
| ME08-02 | v Valid transaction → v Valid transaction | Correct | G09 and valid terms allow versioning at the current revision with coherent balance and cutoff impact. Preserve original identity, currency and type. |
| ME08-03 | v Valid transaction → v Valid transaction | Refine | G09 and a proved nonfuture instant in the original interval allow precision-only versioning. Preserve suspension and obtain a new dependent preview. |
| ME08-04 | v Valid transaction → x Invalidated transaction | Invalidate | G09 and eligible target remove current contribution. Retain identity, audit and originals. |
| ME08-05 | x Invalidated transaction → x Invalidated transaction | Refuse new mutation | Incompatible target state preserves original and invalidation. |

## ME09 · Balance check validity

[Editable UML diagram](diagrams/ME09.svg) · Persistent per observation · CU-012 CU-013 CU-014 CU-015 CU-016 CU-017 CU-018 CU-019 · A04 A05 A06 A12 · I01 I07 I08 I10 I11 · LAC-04 LAC-05 LAC-09

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME09-01 | i Initial → v Current check | Confirm observation | Explicit amount, eligible unambiguous cutoff and current preview freeze amount, author, recording time, cutoff and original under a new ID. Zero transactions. |
| ME09-02 | v Current check → s Superseded check | Replace | G06/G09 require eligible new observation, later revision and no successor. Preserve predecessor, links and separate edge reason. |
| ME09-03 | s Superseded check → s Superseded check | Refuse second replacement | No branch, stale proposal returns to review. Compatible replay belongs to ME11. |

## ME10 · Recovery link

[Editable UML diagram](diagrams/ME10.svg) · Persistent per pair · CU-013 CU-014 CU-015 CU-016 CU-018 CU-020 · A04 A05 A12 · I01 I08 I09 I10 I11 I12 · LAC-04 LAC-05 LAC-09

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME10-01 | i Initial → v Attributed link | Associate | G04 requires current eligible check, confirmed fact and pair, and compatible identity. Existing-link effect is nonmonetary. Distinct expense and originating link commit together once. Succession inherits no link. |
| ME10-02 | v Attributed link → r Withdrawn link | Withdraw | G09 on the mistaken current pair removes attribution. Keep transaction, evidence, link, balance and remaining difference. |
| ME10-03 | r Withdrawn link → v Attributed link | Restore | G04/G09 and current proof allow a separate decision and receipt on the same pair. Restore attribution without creating a new link or transaction. |
| ME10-04 | r Withdrawn link → r Withdrawn link | Refuse restoration | Ineligible proof, changed revision, unavailable baseline or different pair preserves withdrawal. A different pair needs consent of its own. |
| ME10-05 | v Attributed link → v Attributed link | Refuse duplicate association | Preserve link. Eligible replay returns the original result without new effects. |

## ME11 · Authoritative submission outcome

[Editable UML diagram](diagrams/ME11.svg) · Observable durable decision · CU-006 CU-007 CU-008 CU-010 CU-012 CU-013 CU-014 CU-015 CU-016 CU-017 CU-018 CU-019 CU-020 · A01 A08 · I01 I03 I11 I12 I13 · LAC-02 LAC-08

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME11-01 | i Initial → p No definitive outcome | Present protected intent | Validate scope and terms. The model does not require a stored pending state. |
| ME11-02 | p No definitive outcome → c Confirmed | Confirm effect | First send or explicit eligible resubmission with no canonical result revalidates body, scope, revision and barrier. Commit effect, trail, revision and receipt once. Reply loss leaves commitment unchanged. |
| ME11-03 | p No definitive outcome → c Confirmed | Find canonical receipt | Verify original effect or pair and authorized disclosure before fresh execution guards. |
| ME11-04 | p No definitive outcome → p No definitive outcome | Lookup remains inconclusive | Read only, zero writes and no inferred rejection. |
| ME11-05 | p No definitive outcome → r Definitively rejected | Prove definitive rejection | Prove no earlier commitment. Retain facts, trail and revision. Human review may prepare another intent. |
| ME11-06 | c Confirmed → c Confirmed | Replay compatibly | Validate holder, environment, target and terms. Subject to privacy and scope, return original receipt before fresh guards and do not re-execute. |
| ME11-07 | r Definitively rejected → r Definitively rejected | Recover original rejection | Return the authorized no-effect result for this original. |
| ME11-08 | c Confirmed → c Confirmed | Replay incompatibly | Conflict without disclosure of another result or mutation of the original. |

## ME12 · Client knowledge of outcome

[Editable UML diagram](diagrams/ME12.svg) · Client knowledge · CU-006 CU-007 CU-009 CU-010 CU-012 CU-020 · A01 A08 A11 · I01 I03 I12 I13 · LAC-02 LAC-08

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME12-01 | i Initial → h Preparing | Prepare intent | Freeze original terms and scope before network. |
| ME12-02 | h Preparing → f Protection failed before network | Fail before network | No transmission. Allow fixing protection. |
| ME12-03 | h Preparing → p Protected original | Protect original terms | Persist immutable scoped terms and intent identity. |
| ME12-04 | f Protection failed before network → p Protected original | Repair protection | Proved no network and successful protection permit an authorized first send. |
| ME12-05 | p Protected original → p Protected original | Restart before send | With zero transmission proved, restore identical terms under the same holder, environment and target. |
| ME12-06 | p Protected original → w Sent and awaiting proof | Submit deliberately | Verify context and protection before first transmission. |
| ME12-07 | p Protected original → u Uncertain | Restart in an indeterminate position | Possible transmission preserves uncertainty. |
| ME12-08 | w Sent and awaiting proof → u Uncertain | Lose response or restart | Maintain the same original and uncertainty. Block equivalent writes. |
| ME12-09 | w Sent and awaiting proof → l Original lost | Lose original after possible send | Prevent equivalent attempt without original proof. |
| ME12-10 | u Uncertain → l Original lost | Lose local intent while uncertain | No implication of no effect. |
| ME12-11 | l Original lost → u Uncertain | Restore original | Check identical terms and scope. Keep uncertainty until proved. |
| ME12-12 | u Uncertain → u Uncertain | Lookup inconclusive or proof expired | No mutation, duplication or demonstrated rejection. |
| ME12-13 | u Uncertain → u Uncertain | Sign out or change scope | Isolate original from unauthorized access and keep eligible protected intent. |
| ME12-14 | u Uncertain → w Sent and awaiting proof | Explicitly resubmit | Require identical protected original, current authority and eligibility. |
| ME12-15 | w Sent and awaiting proof → k Confirmed outcome known | Prove confirmation | Accept demonstrated result for original and refresh separately. |
| ME12-16 | u Uncertain → k Confirmed outcome known | Recover confirmation while uncertain | Verify canonical result and scope, then refresh separately. |
| ME12-17 | l Original lost → k Confirmed outcome known | Recover confirmation after local loss | Require independent proof linking original and effect, then refresh separately. |
| ME12-18 | w Sent and awaiting proof → r No-effect rejection known | Prove rejection | Require definitive no effect for this original before allowing preparation of another intent. |
| ME12-19 | u Uncertain → r No-effect rejection known | Recover rejection while uncertain | Release preparation only with the proved original outcome. |
| ME12-20 | l Original lost → r No-effect rejection known | Recover rejection after local loss | Require independently demonstrated no effect for the original. |

## ME13 · Validity of a stored reading

[Editable UML diagram](diagrams/ME13.svg) · Client knowledge per reading · CU-005 CU-006 CU-009 CU-011 CU-013 CU-017 CU-018 CU-020 · A01 A05 A08 A10 A11 · I01 I03 I07 I10 I11 I13 · LAC-05 LAC-07 LAC-08

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME13-01 | i Initial → v Valid reading | Receive reading | At publication revalidate session, holder, account, filters, cutoff, revision, origin and known invalidation. |
| ME13-02 | v Valid reading → d Prior reading still eligible | Observe newer revision or inconclusive refresh | Keep only demonstrably valid scoped prior data with reference and stale indicator. |
| ME13-03 | d Prior reading still eligible → v Valid reading | Verify refreshed reading | Publish compatible current response, including confirmed empty. |
| ME13-04 | v Valid reading → x Suppressed | Withdraw current reading | Session, holder, account, filters, revision, privacy transition or known invalidation suppress affected content and incompatible pages. |
| ME13-05 | d Prior reading still eligible → x Suppressed | Withdraw prior reading | Invalid scope or figure suppresses stale reading and late response. |

## ME14 · Sign-in management outcome

[Editable UML diagram](diagrams/ME14.svg) · Client knowledge per act · CU-004 · A02 · I01 I02 · LAC-01

| Edge | From → to | Event | Guard, effect or refusal |
| --- | --- | --- | --- |
| ME14-01 | i Initial → p Management act selected | Choose management act | Request act-specific authority and confirmation for linking, removal or applicable password change. |
| ME14-02 | p Management act selected → c Confirmed | Confirm result | Verified authority and demonstrated original outcome. |
| ME14-03 | p Management act selected → r Canceled or refused | Prove cancelation or refusal | Preserve prior scope and links. |
| ME14-04 | p Management act selected → u Uncertain | Lose response | Stay uncertain, no automatic repeat. |
| ME14-05 | u Uncertain → c Confirmed | Lookup demonstrates success | Read only the original act for the same authorized holder. |
| ME14-06 | u Uncertain → r Canceled or refused | Lookup demonstrates refusal | Read only the authorized original outcome. |
| ME14-07 | u Uncertain → u Uncertain | Lookup inconclusive | Keep uncertainty without mutation. |
