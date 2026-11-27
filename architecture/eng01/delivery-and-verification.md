# Implementation and verification

ENG01 establishes planned evidence. The approved SRS and ER04 remain the owners of RF, IF, RNF, CU and scenario traceability. Design approval, a working synthetic branch, physical Release, operational rehearsal and distribution have different acceptance records.

## Vertical delivery order

Each slice includes iOS, API/DB changes as applicable, fixtures and a reproducible end-to-end oracle. Privacy, version pairing and unresolved design decisions block promotion of an affected slice. Synthetic integration may exercise already specified behavior using approved reference states. Keep gate-dependent screens and pilot participation outside activation until approved.

| Order and slice | Depends on | Demonstration and exit gate |
| --- | --- | --- |
| V0 · Compatibility harness | ENG01 decision review | Clean bootstrap and representative legacy upgrade match expected schema; manifest, readiness and staging Release configuration identify the pair. No product activation. G05/RNF-16/17. |
| V1 · Verified entry and safe exit | V0, provider and identity policy decisions | Native Apple and permitted email/password, verified access, scope, safe logout, reauth and password recovery/link parity as approved. Prove session/holder/credential transitions and negatives, and implement lifecycle fence shared with every writer. RF-001–011 / IF-08/11, G02; deletion completion follows V6. |
| V2 · Opening, Home and ordinary movement | V0–V1 and protected intent protocol | Native official shell in authorized Release staging. Open with explicit BRL base/T0/zone, record an ordinary expense and income, get canonical receipt, read revision. Exercise protected resume, pure query, new session freshness, ambiguity and S01/S07/S08. RF-016/017, 019–022, 042–047, G04/G05/G07. |
| V3 · History and ordinary check | V2, HF03 eligible handoff | Query all authorized rows with stable paging, civil period and type/description filters, detail return and privacy. Record the S02 observation and show IF-13-A–E including tied/unavailable/superseded references. RF-023–027, G04/G07/G08. |
| V4 · Recovery and audited changes | V2–V3; explicit 05.3 and action-by-action 05.2/05.3 decision | 0/1/many, distinct vs existing, defer/reuse and stale preview, evidence/contribution histories; withdrawal/restoration, two reasons, exact time refinement, S03–S05/S09–S11 and base lifecycle. RF-028–041 and RF-042–047. G03/G04/G07/G08, gate-dependent surfaces await Product. |
| V5 · Legacy adoption | V0–V3; eligibility and writer inventory; affected design acceptance | S06 eligibility, explicit identification and preservation, counts/identities/categories checked before and after, distinct unsupported cases and rollback both sides of activation. RF-018/IF-10/12, G05. Must pass or remain inaccessible with preservation and explicit disposition. |
| V6 · Coordinated deletion and restoration | V1 shared fence, V2–V5 writer coverage, approved 05.4 and retention/provider decisions | Race acceptance against every writer; original request lookup after revocation, partial/complete, revocation and protected local disposal; two isolated reconciled restores. RF-012–015, G03/G04/G10. |
| V7 · Release evidence and promotion decision | V0–V6 applicable choices, approved product scope | Physical iPhone Release, both languages and AX5/VoiceOver, all applicable ER04 gates, operational and financial checks on a known pair. G05/G06.2/G09/G10. Independent pilot/production authorization. G06.1/G06.3 retain their deferrals and conditions. |

V5 investigation can overlap V3–V4. Design work for 05.3/05.4 can proceed without changing requirements or promoting financial actions. A partial pilot includes an explicit safety and product decision for each feature that stays inaccessible. Native iOS path keeps existing iPad configuration and initial iPhone priority.

## Test strategy

| Layer / environment | Test oracle and evidence |
| --- | --- |
| Pure unit / CI with synthetic fixtures | Table-driven exact money boundaries/overflow, origin and civil dates, transitions, check selection, validation, Swift Codable, typed errors, compatibility client and context state. Retain Jest and existing XCTest / Swift Testing tests. Use controlled URLProtocol for iOS, deterministic clocks and paused tasks around await. |
| HTTP contract / isolated runner | Compare OpenAPI schema and actual responses against a candidate client. Include old supported pairing, unknown states, missing nullable field behavior, valid/denied auth by method and per resource, pagination, timeouts, conflict, stale cursor and replay response. Record semantic changes in version notes. |
| PostgreSQL integration / disposable DB | Use clean and upgrade fixture paths and actual grants, two connections and controlled ordering. Count logical effects and receipts before/after; verify invariants, original values, cross-owner isolation, revision evolution and key mismatch. Force failures at defined pre/post-commit boundaries using controlled transport or fixture hooks. Never point schema-recreating tests at shared/production DB. |
| iOS/API/DB integration / staging | HTTPS and provider fixtures, default tests disconnected from live systems. Run protected resumption across relaunch, lookup with no writes, explicit replay and holder/session change; compare durable records with UI. Prove build contains no Debug-dependent path; do not use the simulator-erasing script on existing simulators. |
| Physical signed Release / authorized staging | IF-01–13, both locales, compact iPhone, preferred accessibility controls, AX5, VoiceOver, focus/selection, reduced motion/transparency, contrast, AutoFill, Apple/lock/app switch and receipt. Capture build/device/OS/environment and results. No live payload. |
| Operational / isolated staging and restore lanes | G09 latency/load/availability with redacted telemetry by family, G10 two timed restores and rollback/upgrade checks. Stage failure modes before trying participant use. |
| Participant evidence | Separate approval, protocol and consent; use fictitious data, comparable baseline, cohort/denominator, missing data and comparable conditions. M1–M5 remain unverified. |

Use ER04 oracles S01–S11 and ER02 V-01–24. Specific negative suites must cover losing write before commit, winning commit with lost reply, lookup/replay divergence for all 14 S07 targets from ER04, power/Keychain loss, old bearer and concurrent deletion; 23h/25h days, T0 and exact cutoff, S02 values 800/845/+45 in BRL, exact wide signed aggregate, T0-changing S10 and supersession chain. Reconcile with DB snapshots and HTTP/iOS agreement. A fixture result cannot stand for a different version pair or physical device. [NestJS testing guidance](https://docs.nestjs.com/fundamentals/testing).

## V0 compatibility evidence and runbook

### Reproduction

From `lenver-backend`, run `npm run verify:compatibility`. For the complete Docker and simulator checks, set both `VALIDATE_NODE24_DOCKER=1` and `IOS_SIMULATOR_UDID=<UDID-of-an-isolated-booted-simulator>`; without them Docker or simulator results remain `not_run`. `TMPDIR` must be writable. `LENVER_IOS_ROOT` and `LENVER_DOCS_ROOT` are optional checkout-path overrides; sibling folders are discovered by default. `COMPATIBILITY_MANIFEST_PATH=<absolute-existing-writable-directory>/manifest.json` is optional and saves the machine record. The harness builds StagingRelease with the non-live placeholder `https://staging.example.test`; it creates its own PostgreSQL cluster and test databases and does not need `TEST_DATABASE_URL` from the caller.

The harness avoids `npm run lint` because that script invokes ESLint with `--fix`. A separate no-fix repository-wide run found 13 existing findings in `test/apple-auth.integration-spec.ts`; the same 13 reproduce against the baseline file from `HEAD`. The harness lint is limited to the changed backend TypeScript files, and it passed.

When Docker validation is enabled, the harness sets Docker fields to `passed` only after `docker info`, image build, `docker run`, and a Node `v24.*` runtime check all succeed. Docker fields start as `not_run`; externally supplied status/version values are ignored. The script prints a JSON manifest to stdout.

### Readiness and liveness

| Signal | What it proves | What it does not prove |
| --- | --- | --- |
| `GET /health` | The API process answers the configured health request. | Database access, marker compatibility, providers, a complete financial journey or service-level availability. |
| `GET /ready` | A read-only query under bounded pool acquisition (5 s) and statement (1.5 s) timeouts can access the schema marker and it exactly equals `20261126_v0`. A missing row, SQL `NULL`, mismatch or database error returns generic `503 not_ready`. | Migration history, provider availability, per-route permissions, writer barrier, release approval or a real deployment. Runtime DB role needs `SELECT` on the marker table; this was not provisioned or tested against deployed credentials. |

The API version remains OpenAPI `0.0.1` with unversioned and `/mvp` paths. V0 adds no `/v1` finance endpoint. The schema marker is an additive compatibility signal, not a migration ledger. Clean bootstrap includes it. Upgrade applies the new marker migration last.

### Staging Release isolation

Xcode configuration `StagingRelease` builds with a distinct bundle ID and the `LENVER_API_ENVIRONMENT=staging` Info.plist setting. Supply the authorized host explicitly as a build setting:

```sh
xcodebuild -project Lenver.xcodeproj -scheme Lenver \
  -configuration StagingRelease -destination 'generic/platform=iOS' \
  LENVER_STAGING_API_BASE_URL=<authorized-https-staging-host> \
  CODE_SIGNING_ALLOWED=NO build
```

The StagingRelease Bundle ID is fixed as `com.ferrari.Fintech.staging`; it does not vary with the endpoint. The Keychain service namespace starts with `com.ferrari.Fintech.staging.authentication` and appends the full endpoint, so credentials are isolated when the endpoint changes. The angle-bracket value is a placeholder, not a live endpoint.

### Forward recovery and rollback

V0 only adds the schema marker; no finance rows or semantics are changed by the migration. The upgrade and clean-bootstrap commands run on separate disposable databases. The marker migration uses a transaction but is not rerunnable after success: do not replay it by filename. Until a migration ledger is added, an operator must check the actual marker and schema before applying SQL. If a local staging attempt fails, retain the database, collect redacted evidence and recover forward from that exact state. Do not downgrade the schema or restore a snapshot to erase accepted financial outcomes. If any later version writes new facts, binary rollback is allowed only when the earlier API and all clients preserve those facts, receipts, authority and writer compatibility; otherwise stop writes and repair forward. A database rewind requires a separately approved restore/reconciliation procedure.

No schema migration, deploy or rollback was run against an external database in this delivery.

## V1 readiness opinion

ENG01 v1.0, V0 and V0.1 were accepted by the responsible owner on 26/11/2026. V1 planning can use this foundation; V1 implementation and any Release flow remain gated.

## Checklist Master manual regularization matrix

This table records REN01 regularization for the published Checklist Master v18. RQ-008 retains its approved documentary completion and is not downgraded because operational gates remain open. Documentary completion does not close implementation, Release or operational evidence.

| Checklist ID | Documentary disposition | Execution / operational evidence | Manual regularization note |
| --- | --- | --- | --- |
| RQ-008 | **Concluído no escopo documental** by the owner-approved ER04 opinion; preserve the acceptance record. | Runtime, release, participant and operational checks remain independent gates. | Preserve the accepted status in published Checklist v18; do not downgrade it to `Parcial`. |
| PD-009 | **Concluído documentalmente**: premises, restrictions, alternatives and conditional technical conclusion are recorded in the architecture, contract and gate documents. | Capacity, operational staffing and release feasibility are not measured. | Close only the original documentary criterion; preserve measured feasibility gates. |
| PD-010 | Partial: provider price references and cost scenarios are estimates, not an approved budget. | No purchase, invoice, region or complete provider cost was verified. | Keep partial until budget and effective tariffs are approved. |
| PD-011 | Partial: roles, effort and fallback responsibilities are documented as proposals. | No operational capacity or restore/on-call rehearsal. | Keep partial until responsibilities and G10 rehearsal are confirmed. |
| MD-009 | **Concluído documentalmente**: reviewed conceptual entities and relationships are recorded separately from current storage and future targets. | Model review is documentary; target lifecycle tables are not implemented. | Close the conceptual-model criterion only. |
| MD-010 | **Concluído documentalmente**: logical keys, cardinalities and constraints are mapped to the implemented schema and distinguished from target tables. | Clean bootstrap and pinned synthetic I1–I5 upgrade catalogs match (14 tables, 108 constraints, 48 indexes, 27 functions, 23 triggers); upgraded-schema API/readiness and separate clean-schema integration passed. Deployed schema, all legacy histories and runtime grants remain unverified. | Close the schema-documentation criterion; keep deployed proof open. |
| MD-011 | Partial: sequence diagrams cover intent reservation, replay, stale revision, deletion and restore as target behavior, but the full required write/idempotency/conflict/recovery sequence set is not evidenced. | No V1 target routes or deletion/intent tables are implemented; S07 full matrix not run. | Keep partial until every required sequence is explicit and reviewed; do not infer implementation. |
| AR-007 | **Concluído documentalmente**: current component/version identifiers, route boundaries, migration order and known compatibility limits are recorded, including the absence of a formal min/max client matrix. | No deployed pair, old-client matrix or all-writer race proof. | Close the documentation criterion while preserving pair/writer compatibility gates. |
| AR-010 | **Concluído documentalmente**: architecture, approved decisions, conditions and limits are consolidated. | V0 source evidence is local; production topology and provider choice remain unverified. | Close the architecture-document criterion only. |
| AR-011 | Partial: risks, impact, control, residual state and gates are recorded. | Risk-by-risk accountable owners and review are not evidenced. | Keep partial until each risk has a reviewed owner. |
| AR-012 | **Concluído documentalmente**: C4 context, containers and API components are documented. | They describe the approved system model, not an independently verified production topology. | Close the diagram-documentation criterion; retain deployment verification. |
| AR-013 | Partial: environment and staging separation documented/configured. | Generic unsigned `StagingRelease` builds; no authorized endpoint, signing, provider identity or deployment. | Preserve staging and deployment evidence as open. |
| AR-014 | **Concluído documentalmente**: DFD identifies flows, data, external actors and trust boundaries T1–T5. | Live provider flows, logs and copies were not exercised. | Close the DFD criterion only. |
| AR-015 | Não confirmado: S1–S8 threats, controls and residuals are documented. | The original criterion also requires reviewed owners; no per-threat owner approval is evidenced. | Keep open; do not equate the threat table with formal acceptance. |
| AR-016 | Partial: purpose, sensitivity, access boundaries and candidate retention are recorded. | Actual provider-copy locations and deployed log/backup stores are unverified. | Keep partial until locations, access and retention are reconciled with the live inventory. |
| AR-017 | Documented as candidates: proof 15 min, contextual reauth ≤5 min, refresh idle ≤30 days, telemetry 14 days, aggregates 90 days, replay 30 days, deletion lookup 30 days after terminal outcome and synthetic backups 7 days. Absolute refresh limit remains undecided. | No live expiry, purge, provider retention or backup lifecycle was tested. | Keep policy approval and purge proof open; anti-duplicate evidence outlives replay payload. |
| AR-018 | Partial: ASVS 5.0.0 control families and evidence gaps mapped. | No complete control-by-control assessment against a deployed pair. | Keep implementation and evidence mapping open. |
| AR-019 | Partial: MASVS 2.1.0 applicability and gaps mapped. | No signed Release or physical-device assessment. | Keep binary-specific evidence open. |
| AR-020 | Partial: sizing assumptions, workload and targets documented. | No C1/C2 load or resource measurement. | Keep load gate open. |
| DQ-010 | **Concluído documentalmente**: accepted deliveries, V0–V7 dependencies, sequence and gates are recorded in the ENG01 sequence and Roadmap update. | Only V0 compatibility checks ran; no later vertical started. | Close the planning-document criterion; do not regularize V1–V7 execution. |
| DQ-011 | **Concluído documentalmente**: unit/contract/integration/UI-build/simulator/operational levels, environments, synthetic data and entry/exit gates are defined. | Backend suites, disposable DB checks, iOS target compilation and Simulator unit tests passed in the accepted V0.1 evidence; broader races, physical and operational matrices remain unrun. | Close the strategy-document criterion and preserve execution gaps. |
| DQ-012 | Candidate documentation completion: Definition of Ready is documented as a proposal. | Not applied to a later vertical. | Keep process adoption separate from prose completion. |
| DQ-013 | Candidate documentation completion: Definition of Done is documented as a proposal. | Not applied to a later vertical. | Keep process adoption separate from prose completion. |
| DQ-018 | Partial: current evidence and denominator limits are documented. | This delivery records harness duration, one agent, one user clarification and one failed test-double iteration; full task wall time was not instrumented. | Keep a comparable longitudinal baseline open. |
| UX-015 | Pending in Checklist; ENG01 records the accepted HF03 scope and retains 05.3/05.4 gates. | No V1 screen, physical Release or participant evidence was produced here. | Preserve `Pendente` until its existing acceptance criteria are evidenced. |
| UX-018 | Partial in Checklist; eligible HF03 handoff is documented. | No implementation/physical Release validation was run here. | Preserve `Parcial` and the remaining gate conditions. |
| COB-002 | Documented and locally generated: provenance fields, readiness distinction, staging inputs and reproduction steps. | Manifest and harness cover source hashes and disposable DB only; no authorized artifact, provider, runtime role or live readiness. | Record local V0 evidence only; keep external release/configuration proof open. |

Do not close gates from the document update or a local fixture. Preserve every linked design, security, research, Release and operations gate until its own criterion has an artifact.

## Entry and exit criteria

**Ready for implementation.** Accepted requirement and UX scope, owners, dependencies, versioned contract or explicit versioned proposal, impacted data and writers identified, rules and test oracle traced to RF/IF/RNF + CU/S, privacy and rollback boundaries, explicit gates and authorized test environment. An unresolved product/security choice blocks the affected work. Each work item records what evidence will satisfy it.

**Done for a vertical slice.** Satisfies the approved acceptance, code and documentation reviewed, unit/contract/integration checks applicable to the changed boundary passed, required negative and migration scenarios exercised, provenance/reproduction and gaps recorded; failure/rollback behavior documented. Relevant iOS state is rendered and checked through its normal entry path. Remaining Release/product gates are recorded before promotion. An affected integrity or security gate with an unresolved block prevents exposure.

| Gate | Objective promotion check |
| --- | --- |
| G02 | Method-specific authorization, holder exclusivity, recovery & revocation, adversarial session/privacy negatives and physical provider lifecycle. |
| G03 | Correction/disposal contract, contextual proof, originals/audit, atomic effect and deletion fence in each race ordering; partial vs complete outcomes. |
| G04 | Protected scope, pure status, one logical effect despite retry, no replay without explicit call, unknown and local loss fail closed; late results suppressed. |
| G05 | Clean and upgrade runs with expected schema and version-pair compatibility, all writers, verified rollback; eligibility separate from release. |
| G06 | G06.2 physical signed Release evidence; G06.1 and G06.3 remain separate research gates under approved deferrals. |
| G07 | Exact money, full filters and stable history, civil-time precision and consumers proven on candidate pair. |
| G08 | IF-13-A–E authorized native references, server tie-break and unavailable/empty behavior. |
| G09 | C1/C2 and three repeats per protocol. p95 API ≤500 ms readings, ≤800 ms ordinary writes, ≤1,500 ms extensive previews/correction. UI ≤200 ms local feedback, ≤2 s Home/list/receipt, ≤3 s complex preview. C1 attributable failures ≤1% per class, no integrity violation, recovery to C1 thresholds ≤5 min. 30-day 99.5% per family and separate 15-minute probes under ER02 denominators. Device cold start ≤2 s p95, memory ≤200 MiB and ≤20 MiB growth after ten cycles, hitches ≤10 ms/s, no autonomous financial background polling in test window. |
| G10 | Two reconciled isolated restores, no orphans/duplicate/revived authority, affected outcomes known or blocked; RPO ≤1 h and RTO ≤8 h, separately report lost confirmed facts. |

## Evidence reporting and process metrics

Record date/period, source version pair, test profile, check identifier, authorized fixture, expected and actual observations, evidence URI and disposition. Reuse the ER04 link index. Review high-risk diffs and failed scenarios with an independent reader before exposure. An independent read-only review examined the proposed architecture, contracts, financial integrity and security. Its second pass sharpened intent reservation and terminal proof, the independence of receipt payload retention from referential anchors, and the environment-wide fail-closed rule for an unreconciled restore. Product and engineering approval is pending.

DQ-018 stays partial as a recommendation. HF03 reported 32 frames, 40 continuity links, 68 measured targets, eight desk checks and 13 captures in a design-delivery population, with one read-only reviewer and zero reported intermediate Product interventions. The HF02 handoff reported 18 min 18 s for its run and a different volume (90 states and 360 frames). Period and population differ; no speedup, productivity, tokens or cost can be derived. Historic I5 suites are runtime evidence for their historical fixture. Establish separate current process baseline from timestamped start/ready/acceptance, reviewed slices, verification outcomes, rework and blocked time. Report denominators, missingness and comparable work class. Use only aggregate, authorized process data. PRD outcome M1–M5 and ER02 operational metrics stay separate from process throughput.

For this ENG01/V0 delivery, the final machine-recorded compatibility-harness duration is in the JSON manifest. One agent worked the task; one user clarification authorized the narrow concurrency-test repair. The test-double correction had one unsuccessful visibility-only iteration because the helpers are shared across test files; the final correction preserved actor-owned mutable state and the existing async calls. Total human wall-clock duration was not instrumented, so do not infer it from harness duration. No production concurrency code was changed.
