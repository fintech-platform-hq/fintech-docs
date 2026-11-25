# Internal activities and alternative flows

These MF03.1 v1.0.0 views retain the approved activity and node IDs, responsibilities and alternative branches. Each arrow points to the next node ID. The editable SVGs show additional, unlabeled UML merge nodes. See [common guards and commitments](behavior.md).

## AP01 · Authenticate and authorize scope

[Editable UML diagram](diagrams/AP01.svg) · CU-001 CU-005 · ME01 ME02 ME03 ME05 · A02 A11 I01 I02

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Choose registration or password or Apple sign-in | v |
| v | Lenver authority | Valid authentication proof and resolved identity? | Yes → g, Invalid, canceled, expired, ambiguous or network failure → f |
| g | Lenver authority | Admission open and financial scope eligible? | Valid Apple proof or verified password email → a, Barrier or pending password verification → p |
| a | Lenver authority | Authorize the session and own reads. Consume one-use Apple proof | z |
| p | Lenver authority | Deny access under closure. Otherwise allow a verification-only password session | z |
| f | Client | Show the specific response. Suppress prior data and preserve separate identities | z |
| z | — | End | — |

## AP02 · Verify and recover access

[Editable UML diagram](diagrams/AP02.svg) · CU-002 CU-003 · ME01 ME03 ME04 ME05 · A02 I01 I02

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Request purpose-specific proof or choose a usable method | e |
| e | Lenver authority | Return neutral recovery response. Issue proof only when eligible | v |
| v | Lenver authority | Valid unused proof for this holder and purpose? | Yes → c, No or unconfirmed → r |
| c | Lenver authority | Consume proof and commit verification or access recovery for the same holder | t |
| t | Client | Recheck scope after verification. After recovery return to sign-in | z |
| r | Client | Keep access guard and offer eligible proof. Zero usable methods remains LAC-01 | z |
| z | — | End | — |

## AP03 · Link a sign-in method

[Editable UML diagram](diagrams/AP03.svg) · CU-004 · ME01 ME02 ME03 ME14 · A02 I01 I02

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Choose linking or another management act | t |
| t | Account holder | Is this a voluntary link? | Link → p, Change password, remove or query an outcome → o |
| p | Account holder | Prove control of both methods and consent | v |
| v | Lenver authority | Proofs, account and exclusivity valid at commitment? | Yes → c, Insufficient proof, cancelation or conflict → r |
| c | Lenver authority | Commit exclusive link to the same holder | e |
| e | Client | Show authorized result or use AP15 after reply loss | z |
| r | Client | Refresh authorized state without merging or granting access | z |
| o | Client | Route management and result lookup to AP15 | z |
| z | — | End | — |

## AP04 · Unlink, revoke and sign out

[Editable UML diagram](diagrams/AP04.svg) · CU-004 CU-005 CU-006 CU-020 · ME02 ME05 ME12 ME13 · A02 A08 I01 I02 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | v |
| v | Lenver authority | Is this a proved external revocation? | External revocation confirmed → e, Authorized local action → t |
| e | Lenver authority | Invalidate external proof. Zero methods may remain. Sessions follow LAC-01 | z |
| t | Account holder | Is the local goal sign-out? | Sign out → l, Unlink method → d |
| l | Client | End local authority, hide data and intents, and preserve eligible protected intent | m |
| m | Lenver authority | Determine remote revocation separately. Reply loss leaves local access closed | z |
| d | Lenver authority | Does another method remain usable at commitment? | Yes, with valid proof and account → c, No or concurrent act won → r |
| c | Lenver authority | Unlink and handle sessions under LAC-01. Reply loss goes to AP15 | z |
| r | Client | Show remaining authorized methods and preserve existing links | z |
| z | — | End | — |

## AP05 · Accept deletion and coordinate effects

[Editable UML diagram](diagrams/AP05.svg) · CU-007 CU-020 · ME01 ME05 ME06 ME11 ME12 · A01 A08 I01 I03 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Review consequences, reauthenticate and decide | v |
| v | Account holder | Explicitly confirmed? | Confirmation and valid reauthentication → p, Canceled or reauthentication missing → x |
| p | Client | Protected original lookup authority established? | Proof available before send → e, No usable proof → x |
| x | Client | Stop before sending. Preserve access and data and distinguish cancelation from preparation failure | z |
| e | Client | Send the protected original deletion request | g |
| g | Lenver authority | Eligible at the acceptance barrier? | Compatible account and proof, admission open → b, Definitive rejection of original → r |
| b | Lenver authority | Accept and close admission together. Include prior commits and reject later incompatible effects | d |
| r | Lenver authority | Preserve prior admission and record recoverable rejection without effect | o |
| d | Lenver authority | Coordinate sessions, data, local disposal and Apple. Record progress, partial or completion | o |
| o | Client | Authoritative response for this original received? | Original result demonstrated → k, Absent or inconclusive on either branch → u |
| k | Client | Show only request status. Completion requires proof of all applicable effects | z |
| u | Client | Keep uncertainty and query through the protected proof in AP06 | z |
| z | — | End | — |

## AP06 · Query deletion after response loss

[Editable UML diagram](diagrams/AP06.svg) · CU-007 · ME01 ME06 ME12 · A01 I01 I03 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | q |
| q | Client | Request only the original result with proof established before transmission | v |
| v | Lenver authority | Valid proof for original request, holder and environment? | Yes, including invalidated session → d, Absent, other scope, expired or unproved → r |
| d | Lenver authority | Authoritative outcome for the original? | In progress, partial, completed or rejected → c, Indeterminate or response lost → u |
| c | Client | Show minimum protected result and disclose zero account or financial access | z |
| u | Client | Remain uncertain without opening a new request | z |
| r | Lenver authority | Refuse disclosure and preserve current session scope | z |
| z | — | End | — |

## AP07 · Open a baseline or assess legacy adoption

[Editable UML diagram](diagrams/AP07.svg) · CU-008 CU-009 CU-011 CU-020 · ME07 ME11 ME12 · A03 A06 A07 A08 I01 I04 I05 I06 I07 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Lenver authority | Check scope, baseline existence and new or legacy status | b |
| b | Lenver authority | Missing baseline in an eligible context? | New or enabled and proved legacy → t, Existing baseline or unproved legacy → r |
| t | Account holder | Declare BRL before T0 and review zone, coverage and preview | v |
| v | Account holder | Unambiguous preview confirmed? | Explicit amount, valid terms and consent → e, Refine, review or defer → p |
| e | Lenver authority | Use AP12 to commit first account and baseline together, or preserve IDs and pre-coverage history in adoption | o |
| o | Client | Receive AP12 result, inspect winner and refresh after confirmation | z |
| p | Client | Ask for precision or defer without writing. Refresh preview on return | z |
| r | Client | Suspended goes to CU-018. Blocked legacy retains authorized history without a figure | z |
| z | — | End | — |

## AP08 · Record or change a transaction

[Editable UML diagram](diagrams/AP08.svg) · CU-010 CU-011 CU-015 CU-019 CU-020 · ME07 ME08 ME11 ME12 · A06 A07 A08 A10 I01 I04 I05 I06 I07 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Refining an existing occurrence? | CU-019 refinement → p, Create, correct or invalidate → v |
| p | Lenver authority | Eligible refinement including during suspension? | Unambiguous nonfuture instant in original interval, other terms preserved → f, Authority or precision proof missing → r |
| v | Lenver authority | Authorized change with baseline available where required? | Eligible BRL and occurrence, G09 on correction → f, Ambiguous or incompatible target or baseline → r |
| f | Account holder | Review effect and revision, give applicable reason and confirm for AP12 | a |
| a | Lenver authority | Use AP12 to record, version, invalidate or refine. Refinement preserves suspension | o |
| o | Client | Receive AP12 result. After refinement, replace dependent preview at new revision | z |
| r | Client | Ask for precision, review or defer without creating a version | z |
| z | — | End | — |

## AP09 · Record or replace a balance check

[Editable UML diagram](diagrams/AP09.svg) · CU-012 CU-013 CU-016 CU-019 CU-020 · ME07 ME09 ME10 ME11 ME12 · A05 A06 A07 A08 A12 I01 I05 I06 I08 I10 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Enter a real observation or propose a reasoned replacement | v |
| v | Lenver authority | Cutoff, coverage, amount and target allow preview? | Yes, all relevant facts classify unambiguously → c, Ambiguity or other ineligibility → q |
| q | Lenver authority | Can precision of an existing fact resolve the cutoff? | Yes, CU-019 through AP08 → p, Other impediment or defer → r |
| p | Client | Offer authorized refinement or deferral. Create fresh preview after refinement | z |
| c | Account holder | Review comparison and revision. Use G09 for replacement and confirm | g |
| g | Lenver authority | AP12 commits new identity and original. Replacement requires no successor and commits the replacement edge together | o |
| o | Client | Receive AP12 result and reread without overwriting the old observation | z |
| r | Client | Show unavailability or deferral and preserve observations | z |
| z | — | End | — |

## AP10 · Recover retroactive expense

[Editable UML diagram](diagrams/AP10.svg) · CU-013 CU-014 CU-016 CU-020 · ME08 ME09 ME10 ME11 ME12 · A04 A05 A08 A12 I01 I08 I09 I10 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Review comparison and identify fact and proof. Similarity proposes candidates | v |
| v | Lenver authority | Owned current calculable check with identifiable evidence? | Yes → t, No, including conflicting reference → r |
| t | Account holder | Confirm an existing candidate as the same fact? | Existing expense → e, Distinct expense or deferral → d |
| e | Lenver authority | Expense effective at cutoff and pair eligible? | Open with evidence, or match with nonzero original and proved contribution → k, Zero original, missing match contribution or incompatible pair or proof → r |
| k | Account holder | Confirm existing pair, including explicit eligible reuse. No new fact | a |
| d | Lenver authority | Open discrepancy and valid distinct fact at cutoff? | Identifiable nonduplicate in coverage, confirmation requested → n, Match, outside cutoff, insufficient proof or defer → r |
| n | Account holder | Review and confirm the distinct expense and originating link | a |
| a | Lenver authority | AP12 confirms existing link with no monetary change or distinct expense and link together | o |
| o | Client | Receive AP12 result for requested effect and pair. Reread balance and residual separately | z |
| r | Client | Reject or defer and preserve facts and links | z |
| z | — | End | — |

## AP11 · Suspend figures or correct the baseline

[Editable UML diagram](diagrams/AP11.svg) · CU-009 CU-017 CU-018 CU-019 CU-020 · ME07 ME08 ME09 ME10 ME11 ME13 · A05 A06 A07 A08 A12 I01 I05 I06 I07 I10 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Request suspension or declare new terms with reason, contextual proof and confirmation | v |
| v | Lenver authority | Authority and preview valid with unambiguous terms when applicable? | Reauthenticated holder, reason, expected revision and clear boundaries → c, Missing authority, conflict or ambiguous interval → q |
| q | Lenver authority | Can an existing occurrence be refined for the new T0? | Authorized AP08 refinement even under suspension → p, No eligible refinement or defer → r |
| p | Client | Ask for precision or defer. Keep suspension and preview the new revision | z |
| c | Account holder | Review impact and confirm protected original through AP12 | g |
| g | Lenver authority | AP12 preserves terms in suspension or commits a new version. Moving T0 gives no attribution | o |
| o | Client | Receive AP12 result, reread eligible figures and keep original comparisons | z |
| r | Client | Reject or defer, preserve terms and suppress invalid derivatives | z |
| z | — | End | — |

## AP12 · Protect, confirm and resume a submission intent

[Editable UML diagram](diagrams/AP12.svg) · CU-006 CU-007 CU-008 CU-010 CU-012 CU-013 CU-014 CU-015 CU-016 CU-017 CU-018 CU-019 CU-020 · ME05 ME06 ME11 ME12 · A01 A08 I01 I03 I11 I12 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | a |
| a | Client | Pure lookup of original? | Read outcome only → q, First submission or explicit resubmission → m |
| q | Lenver authority | Authorized lookup scope and outcome? | Holder, environment, target and effect authorized → c, Missing or mismatched → r |
| c | Client | Canonical outcome demonstrated? | Proved original receipt or rejection → k, Inconclusive → u |
| m | Client | Is context authorized for this holder, environment and target? | Same authorized scope → p, Invalid session or mismatched scope → r |
| p | Client | Protected original terms intact? | Same key, body, holder, environment, target and effect → s, Protection or terms unavailable → l |
| l | Client | Proved failure before any network use for this intent? | Zero transmission proved → f, Possible transmission or inaccessible original → u |
| f | Client | Return to form and fix protection before trying | z |
| s | Client | Is this a deliberate first submission? | Original not yet sent → h, Explicit resubmission requested → j |
| j | Client | Explicit resubmission eligible and original intact? | Yes in same scope → h, No consent, proof or eligibility → u |
| h | Lenver authority | Authorized scope and outcome access? | Yes, including effect identity → b, Access denied → r |
| b | Lenver authority | Same key with incompatible body or conflicting reference? | Conflict → x, Compatible identity and terms → e |
| x | Lenver authority | Refuse without mutating the original or authorizing its replacement | z |
| e | Lenver authority | Definitive canonical outcome for same effect? | Yes, before fresh execution guards → o, Absent. Receipt for fact or other pair is evidence only → g |
| g | Lenver authority | Commit authority, revision and barrier permit writing? | Common and caller-specific guards pass → w, Fresh execution refused → n |
| w | Lenver authority | Commit caller effect, trail, revision and receipt once | o |
| n | Lenver authority | Determine definitive absence of effect or preserve uncertainty | o |
| o | Client | Response proves the original outcome? | Receipt or definitive rejection proved → k, Lost or insufficient proof → u |
| k | Client | Return confirmed or rejected to caller. Financial refresh is separate | z |
| u | Client | Return indeterminate, isolate original and preserve eligible intent. Block equivalent attempts | z |
| r | Client / Lenver authority | Refuse lookup or submission and suppress out-of-scope data | z |
| z | — | End | — |

## AP13 · Read and present a coherent result

[Editable UML diagram](diagrams/AP13.svg) · CU-005 CU-009 CU-011 CU-013 CU-017 CU-018 CU-020 · ME05 ME07 ME09 ME12 ME13 · A05 A06 A10 A11 I01 I05 I06 I07 I10 I11 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Client | Capture session, holder, account, filters, cutoff and expected revision | v |
| v | Lenver authority | Read authorized for holder and account? | Yes → q, Denied or invalid session → r |
| q | Lenver authority | Produce coherent response bound to request and revision | g |
| g | Client | Response valid immediately before presentation? | Compatible scope, filters, cutoff and revision. No known invalidation → l, Failure, changed context or known invalidation → j |
| l | Client | Present data or confirmed empty. Preserve originals and suppress unavailable amounts | z |
| j | Client | Is prior reading still authorized, valid and traceable in current context? | Revalidated before display → d, Invalid, unauthorized or unknown origin → r |
| d | Client | Show as stale with reference. Restart incompatible pages retaining authorized filters | z |
| r | Client | Discard late response and suppress affected content. Distinguish error, expiry and unavailability | z |
| z | — | End | — |

## AP14 · Correct or restore a recovery link

[Editable UML diagram](diagrams/AP14.svg) · CU-013 CU-014 CU-016 CU-020 · ME08 ME09 ME10 ME11 ME12 · A04 A05 A08 A12 I01 I08 I09 I10 I11 I12

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Account holder | Inspect trail, evidence and pair. Propose reasoned decision with expected revision | t |
| t | Lenver authority | Withdrawing a current mistaken attribution? | Authorized holder and same active pair → q, Proposed restoration → v |
| q | Account holder | Review and confirm withdrawal while preserving expense and evidence | g |
| v | Lenver authority | Same withdrawn pair and current calculable check with eligible proof? | Authorized holder, G04 and G09 → c, Conflicting proof, pair or state ineligible → r |
| c | Account holder | Review and explicitly restore as a new decision on the same pair | g |
| g | Lenver authority | AP12 commits audited pair decision or recovers its own receipt without repeating effect | o |
| o | Client | Receive AP12 result and reread attribution with balance and residual unchanged | z |
| r | Client | Review proof. A different pair requires its own AP10 decision | z |
| z | — | End | — |

## AP15 · Manage password and method outcomes

[Editable UML diagram](diagrams/AP15.svg) · CU-004 · ME02 ME03 ME05 ME14 · A02 I01 I02

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Client | Is this an outcome lookup for a management act? | Read-only lookup → u, New act → a |
| u | Lenver authority | Authorized for original act and method? | Authenticated holder with outcome authority → q, Missing or different scope → r |
| q | Client | Show demonstrated result or keep uncertainty, with zero mutations | z |
| a | Account holder | Applicable password change? | Usable password method → p, Voluntary unlinking → d |
| p | Lenver authority | Valid contextual proof and proposed password at commitment? | Correct holder and method, current proof and confirmation → c, Canceled, invalid, expired or refused → r |
| c | Lenver authority | Change credential for same holder. Session policy follows LAC-01 | o |
| d | Lenver authority | Apply AP04 and revalidate the last usable method | o |
| o | Client | Outcome demonstrated? | Authorized confirmation or refusal → k, Missing or incomplete response → n |
| k | Client | Show outcome and remaining authorized methods | z |
| n | Client | Keep uncertainty and use separate lookup. Avoid automatic repeat | z |
| r | Client | Keep prior state with no additional disclosure or authorization | z |
| z | — | End | — |

## AP16 · View the Lenver account, privacy and return

[Editable UML diagram](diagrams/AP16.svg) · CU-005 · ME05 ME13 · A02 A11 I01 I13

| Node | Responsibility | Action or decision | Next or alternatives |
| --- | --- | --- | --- |
| i | — | Entry | s |
| s | Client | Opened in an authorized context of this holder? | Valid origin and session → a, Expired, different holder or unavailable → h |
| a | Client | Open from Home in native hierarchical sheet. Offer identity, methods, privacy and support | v |
| v | Client | Is exposure still authorized? | Session, holder and privacy transition allow → t, Background, app switcher, expiration or holder changed → h |
| t | Account holder | Return to the originating context? | Return → g, Read a group or choose an applicable action → e |
| g | Client | Is the origin still authorized? | Same revalidated context → o, Context changed or expired → h |
| o | Client | Restore authorized origin without mutation | z |
| e | Client | Show own information or route methods, sign-out and deletion to their flows | z |
| h | Client | Hide sensitive content and intents in transition. Show expiry or unavailability where applicable | z |
| z | — | End | — |
