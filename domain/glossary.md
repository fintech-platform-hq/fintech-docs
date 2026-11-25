# Bilingual domain glossary

MF01.1 canonical en-US and pt-BR terms. Concept IDs refer to the [approved domain model](functional-model.md#mf011-domain).

| en-US | pt-BR | Concept | Definition and usage |
| --- | --- | --- | --- |
| Account holder | Titular | C01 | Stable person identity that owns the Lenver account and its data. In access contexts, use authorized user when appropriate. |
| Lenver account | Conta Lenver | C01 | The holder's identity, access, privacy and closure context. Qualify “account” when ambiguity is possible. |
| External identity | Identidade externa | C02 | Stable identity asserted by an access provider. Contact can be relay or absent. |
| Sign-in method | Método de acesso | C02 | Linked way to identify the holder for sign-in or access recovery. |
| Usable sign-in method | Método utilizável | C02 | A linked method whose valid proof currently authorizes access. Linkage describes relationship, usability describes capacity. |
| Verified email | E-mail verificado | C02, C13 | Address whose control was proved for password access. |
| Lenver session | Sessão Lenver | C03 | Temporary authorization associated with one holder. |
| Reauthentication | Reautenticação | C13 | Fresh proof of the same holder for a sensitive action or resumption. |
| Account access recovery | Recuperação de acesso | C13 | Restoration of sign-in for the same holder. Name the object when using “recovery”. |
| Voluntary unlinking | Desvinculação voluntária | C02, C13 | Holder-initiated method removal that leaves another usable method. |
| External authorization revocation | Revogação externa | C13 | A provider action that removes authorization, such as Apple revocation. |
| Lenver account deletion | Exclusão da Conta Lenver | C13 | Confirmed request to coordinate closure, access and identifiable holder data. |
| Financial account | Conta financeira | C04 | Personal BRL account for manual financial tracking. |
| Legacy financial account | Conta financeira legada | C04 | Existing owned account and history predating the current baseline or adoption process. |
| Legacy adoption | Adoção legada | C04 | Controlled inclusion of eligible legacy records subject to proof and writer compatibility. |
| Financial baseline | Base financeira | C05 | Declared opening balance, coverage start and reference time zone. |
| Opening balance | Saldo inicial | C05 | Signed amount declared immediately before T0. Explicit zero has meaning. |
| Missing baseline | Base ausente | C05 | No current baseline terms available for calculation. |
| Baseline suspension | Suspensão da base | C05, C11 | Audited withdrawal of availability for figures while retaining baseline terms. |
| Coverage start | Início da cobertura | C06 | T0, the inclusive beginning of coverage. The declared balance immediately precedes it. |
| Time coverage | Cobertura temporal | C06 | Time range in which valid occurrences contribute to the calculation. |
| Balance check cutoff | Corte da conferência | C06, C08 | Observation's temporal reference, inclusive for its calculation. |
| Occurrence | Ocorrência | C06, C07 | When a transaction happened, as an exact instant or a civil-day interval when time is unknown. |
| Recording time | Instante de registro | C06 | When Lenver recorded a fact, distinct from occurrence and cutoff. |
| Money | Dinheiro | C06 | Exact BRL amount represented in cents in the MVP. |
| Transaction | Movimentação | C07 | An income or expense with stable identity, occurrence and amount. |
| Income | Entrada | C07 | Transaction whose effect increases the calculated balance. |
| Expense | Despesa | C07 | Transaction whose effect reduces the calculated balance. |
| Balance check | Conferência de saldo | C08 | Manual observation comparing a reported balance to Lenver's calculation at the same cutoff. |
| Reported balance | Saldo informado | C08 | Signed amount supplied by the holder for the observation. |
| Original comparison | Comparação original | C08, C09 | Immutable original calculated balance, reported balance and difference at cutoff. |
| Original calculated balance | Saldo calculado original | C08, C09 | Lenver's calculation at the check cutoff when that check was confirmed. |
| Current calculated balance | Saldo atual | C04, C09 | Derived amount at a distinct current cutoff and coherent revision. |
| Recalculated balance at cutoff | Saldo recalculado no corte | C09 | Revised calculation at the original check cutoff when available. |
| Original difference | Diferença original | C09 | Original calculated balance minus reported balance. |
| Remaining difference | Diferença restante | C09 | Recalculated balance at cutoff minus reported balance, when available. Residual is contextual shorthand. |
| Balance discrepancy | Divergência | C09 | A nonzero difference that is currently open for review. |
| Open balance check | Conferência pendente | C09 | A current check with a calculable open discrepancy, including partial explanation. |
| Numerical match | Coincidência numérica | C09 | Zero remaining difference without the complete proof criteria for attribution. |
| Attributed resolution | Resolução atribuída | C09 | Proved contribution cancels a nonzero original difference and yields zero remaining. |
| Partial explanation | Explicação parcial | C09 | Relevant contribution reduces the magnitude without changing the original sign or reaching zero. |
| Reopened discrepancy | Reabertura de divergência | C09 | Return of nonzero difference with evidence of prior attributed resolution in the same check. |
| Difference after a numerical match | Retorno após coincidência | C09 | Return of nonzero difference after the applicable proved zero was a numerical match. |
| Match candidate | Candidato à correspondência | C10 | A possible matching fact for human review. Similarity confers no authority. |
| Recovery evidence | Evidência de recuperação | C10 | Identifiable evidence for a fact or linkage. A purchase receipt is distinct from a confirmation receipt. |
| Source reference | Referência de origem | C10 | Genuine source and external identifier scoped to an account, when provided. |
| Recovery link | Vínculo de recuperação | C10 | Auditable association between one balance check and one expense in the same account. |
| Originating recovery link | Vínculo de origem | C10 | Exactly one link created together with a distinct expense recorded by recovery. |
| Link correction | Correção de vínculo | C10, C11 | Audited change withdrawing mistaken attribution while preserving transaction, link and evidence. |
| Expense recovery | Recuperação de despesa | C10 | Finding and deliberately linking an existing expense or recording a distinct retroactive expense with its originating link. |
| Account revision | Revisão da conta | C11 | Reference to one coherent state of the financial account. |
| Version | Versão | C11 | Historical state of a fact, with previous states retained. |
| Preview | Prévia | C11 | Proposed effect for review at a known revision, subject to revalidation. |
| Correction | Correção | C11 | Audited rectification preserving identity and original history as applicable. |
| Invalidation | Invalidação | C11 | Audited removal of an incorrect transaction's contribution while retaining its trail. |
| Replacement | Substituição | C08, C11 | Correction of an erroneous observation via a successor with a new identity and original comparison. |
| Replacement link | Elo de sucessão | C08, C11 | Directional audited edge from the superseded observation to its successor, with its own reason. |
| Superseded balance check | Conferência substituída | C08, C11 | Historical check removed from the active queue, with original and links preserved. |
| Occurrence refinement | Refinamento da ocorrência | C06, C11 | Audited precision increase within the existing occurrence interval while preserving other terms. |
| Submission intent | Intenção de envio | C12 | Protected identity and original terms of a contemplated write, scoped to holder, environment and target. |
| Confirmation receipt | Recibo de confirmação | C12 | Canonical evidence of the original write outcome, specific to its effect or pair. |
| Uncertain outcome | Resultado incerto | C12 | Client lacks proof of confirmation or definitive no-effect rejection for the original. |
| Submission recovery | Recuperação de envio | C12 | Determining the original outcome with authorized lookup and eligible explicit resubmission. |

## Approved UX01 labels

| Usage | en-US | pt-BR |
| --- | --- | --- |
| Home tab | Home | Início |
| History tab | Transactions | Movimentações |
| Review tab | Review | Conferências |
| Review screen title and tab fallback if comprehension warrants | Balance Checks | Conferências de saldo |
| Primary action in Review | Check Balance | Conferir saldo |
| Exclusive type filters | All, Income, Expenses | Todas, Entradas, Despesas |

en-US is the base and fallback, with complete pt-BR coverage. Preserve BRL, sign, date, actual precision and time zone. Use the native segmented control as the initial type-filter reference (UX01-11.1). Comprehension and accessibility retain separate criteria (UX01-08.1 and UX01-17).

## Candidate labels for later validation

| Context | en-US | pt-BR |
| --- | --- | --- |
| Access and finance | Account / Financial account | Conta / Conta financeira |
| Missing and suspended baseline | Set up your balance / Financial baseline suspended | Configurar saldo / Base financeira suspensa |
| Opening figure | Opening balance | Saldo inicial |
| Current, reported and recalculated figures | Current balance / Reported balance / Recalculated balance at cutoff | Saldo atual / Saldo informado / Saldo recalculado no corte |
| Difference, numerical match and attributed resolution | Remaining difference / Balances match / Resolved by linked transactions | Diferença restante / Saldos coincidem / Resolvida por movimentações vinculadas |
| Reopened or returned difference | Difference reopened / Difference returned | Diferença reaberta / Diferença voltou |
| Historical check | Superseded | Substituída |
| Evidence and attribution | Recovery evidence / Remove attribution | Evidência de recuperação / Retirar atribuição |
| Submission and receipt | Confirmation pending / Check submission status / Confirmation details | Confirmação pendente / Consultar resultado do envio / Detalhes da confirmação |
| Day-only occurrence | Time unknown | Hora desconhecida |
| Deletion and result lookup | Deletion in progress / Check deletion status | Exclusão em andamento / Consultar resultado da exclusão |

Preserve separate meanings for account scope, time reference, unavailable and available zero, numerical match and attribution, and purchase evidence and confirmation receipt. Review candidate labels in both languages using the integrated journey.
