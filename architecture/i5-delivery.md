# I5 · Correção auditada da base inicial

A base é o saldo imediatamente anterior ao início da cobertura (T0). Na revisão vigente, o PostgreSQL soma a base assinada às entradas e subtrai as despesas válidas entre T0 e o corte, com as duas fronteiras inclusivas. A conta continua em BRL e mantém a zona de referência informada na abertura. A base e os valores em centavos são transportados em strings decimais, inclusive quando a soma ultrapassa Int64.

[Baseline v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/4391010) · [Decisões D1–D3](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/98326) · [Proposta de arquitetura](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/5898244) · [ADR 0006](../adr/0006-mvp-account-revision-and-coverage.md) · [Entrega I4](i4-delivery.md)

## Contratos

| Operação | Regras e efeitos |
| --- | --- |
| `POST /mvp/audit-previews` | Reaproveita o contrato I4. Para `suspend_base` e `correct_base`, `targetId` é a própria conta. As prévias mostram termos, saldo e diferenças antes e depois. A correção também lista as mudanças de cobertura e os IDs dos movimentos que precisam de hora. |
| `POST /mvp/audited-changes` | As ações do I5 exigem `confirmed: true`, motivo, `expectedRevision`, `previewToken` e `Idempotency-Key`. A mesma intenção com a mesma chave reapresenta o recibo antes de avaliar a revisão corrente. Outra intenção com a chave usada retorna 409. |
| `suspend_base` | Registra autoria, data e motivo da suspensão. As cifras vigentes ficam nulas. O histórico dos fatos e os valores originais das conferências continuam consultáveis. Somente a correção da base ou o refinamento da hora de um registro é admitido enquanto a base está suspensa. |
| `correct_base` | Exige `baseMinor` assinado. A omissão de `coverageStart` preserva T0 até o microssegundo. Ao mudar T0, o titular declara a posição imediatamente anterior ao novo instante. A nova versão reativa o cálculo e o histórico passa a indicar a cobertura vigente de cada fato. |
| `refine_occurrence` | Recebe o ID do movimento e o instante preciso em UTC dentro do intervalo já registrado. Preserva valor, descrição, tipo, vínculo e registro original. Uma nova prévia da base é necessária após a mudança de revisão. |
| `GET /mvp/account/{accountId}/base-versions` | Lista a base original, as suspensões e as correções, da revisão mais recente à mais antiga. Cada versão identifica autor, hora do servidor, termos, revisão e motivo quando aplicável. Recurso inexistente, sem base ou de outro titular responde 404. |
| `GET /mvp/account/{accountId}/checks` | A visão completa devolve observação e resultado originais. Enquanto suspensa ou para um corte anterior ao T0 vigente, os derivados daquele corte são nulos e o estado é `unavailable`. O filtro de pendências considera apenas cortes recalculáveis. |

Quando T0 cruza um intervalo sem hora, qualquer valor projetado que dependa desse movimento fica indisponível. A prévia devolve `previewToken: null` até a resolução. O início do intervalo é inclusivo e a última ocorrência possível conta se for exatamente T0. O fluxo permite suspender a base enquanto o titular investiga e fornece uma hora verificada. A UI coleta dia e hora em UTC para tornar o instante explícito, inclusive quando o horário civil local se repete.

As conferências retêm cálculo, diferença, data e valor informados na revisão original. A atribuição de recuperação usa a cobertura da base registrada naquela revisão para o efeito original. Mudanças de enquadramento devidas a T0 ficam de fora do crédito de recuperação. Os vínculos continuam registrados.

## Migração e operação

Aplicar `migrations/20261123_correct_mvp_opening_balance.sql` após a migração I4 e antes de iniciar a API I5. Para uma instalação limpa, usar `db.sql`. O upgrade copia a abertura de cada conta MVP ativa para a primeira versão. Triggers mantêm as versões imutáveis, serializam a aplicação sob lock da conta e exigem o recibo canônico no commit. As tabelas de movimentos, conferências e recibos existentes mantêm os fatos originais. A migração pressupõe o schema de propriedade aceito nos incrementos anteriores.

Implantar API e esquema juntos. A API mantém os contratos anteriores para estados já representáveis. A suspensão e a mudança de T0 introduzem resultados recalculados nulos e o estado `unavailable` em conferências. Atualizar o cliente iOS antes de liberar a correção para clientes que exigem strings de saldo recalculado. O app permanece no opt-in Debug. Adoção de conta legada segue bloqueada até a auditoria de dados e escritores.

A intenção usa o armazenamento protegido do I1–I4. O app grava o corpo e a chave antes de enviar, valida ambiente, titular, conta e geração de sessão, e recarrega a leitura autoritativa ao resolver o envio. A operação permanece pendente quando não há desfecho definitivo.

## Aceite e reprodução

| Critério | Verificação |
| --- | --- |
| S06 · Histórico legado e corrida de abertura | Testes de upgrade no PostgreSQL preservam dados e recibos, mostram saldo indisponível sem base e confirmam uma única abertura concorrente. A etapa de adoção continua bloqueada. |
| S10 · Correção de R$ 900,00 para R$ 910,00 | A confirmação produz saldo de R$ 837,00 e resíduo de +R$ 55,00. O original permanece R$ 845,00 calculado e +R$ 45,00 de diferença. |
| S11 · Mudança de T0 e hora ausente | A prévia lista os movimentos a desambiguar. Após o refinamento um lançamento no novo T0 entra na soma, o pré-base permanece no histórico e um corte anterior fica indisponível. |
| Q01/Q02 · Precisão, atomicidade e reenvio | Confrontar contagem e revisão no PostgreSQL em caso de concorrência e rollback. Na fixture HTTPS, descartar a resposta após commit e comparar chave, bytes do corpo e recibo no replay. |
| Q03/Q04 · Trilha, identidade e acessibilidade | Inspecionar os valores originais e a trilha durante e após a suspensão. Trocar de titular antes de liberar o replay. Verificar a jornada com o maior Dynamic Type e conferir as falas do VoiceOver em iOS 27. |

Na API, execute `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e `npm run test:integration`. Para a última suíte, `TEST_DATABASE_URL` deve apontar para um banco PostgreSQL 16 descartável com nome terminado em `_test`, pois a suíte recria o schema.

Para reproduzir o HTTPS, inicialize outro banco descartável com `db.sql`. Configure `DATABASE_URL` em loopback, `I5_TLS_DIR` com `localhost.pem` e `localhost.key`, `I5_SEED=1` e credenciais JWT fictícias. Compile e rode `node test/fixtures/i5-https.cjs`. A fixture usa a porta 55465 e libera o replay após a troca de sessão. Confie a CA local apenas nos simuladores dedicados.

No iOS, use o scheme `Fintech`, idioma de teste `en`, região `BR`, `TEST_RUNNER_FINTECH_I5_HTTPS_TEST=1` e os dois métodos HTTPS do I5 em `FintechUITests/MvpAccountIntegrationTests`. A sessão de isolamento e a primeira abertura usam titulares fictícios distintos. Preserve a fixture para rever as versões com `TEST_RUNNER_FINTECH_I5_HISTORY_TEST=1` e `testI5ReadsBaseVersionDetailsAtMaximumDynamicType` no iOS 26.5, e para rodar `testI5VoiceOverReadsCorrectedBalanceAndOriginalDifference` com `TEST_RUNNER_FINTECH_I5_VOICEOVER_TEST=1` e iOS 27.2. Uma nova rodada completa começa com banco vazio e sem pendências locais da rodada anterior.

A proteção de dados em um iPhone físico bloqueado exige validação em aparelho. As suítes e fixtures usam titulares e lançamentos fictícios.

## Verificação local de 23 de novembro de 2026

- API TypeScript compilada. ESLint dos TypeScript alterados passou e as 169 referências internas do OpenAPI foram resolvidas. Passaram 168 testes unitários, 48 testes HTTP e 100 testes de integração no PostgreSQL 16.14. A integração cobriu instalação limpa, upgrade com dados existentes, rollback do recibo incompatível, concorrência, replay, ordens de revisão de um e dois dígitos e corte inclusivo.
- Passaram o build Debug, o build Release genérico e 121 testes Swift. Duas jornadas no iOS 26.5 concluíram abertura, registro, conferência e recuperação retroativa em titulares fictícios, além de suspensão, correção e leitura do histórico da base. A API efetivou a correção antes da perda controlada da resposta. O replay após reinício e troca de titular preservou chave, bytes e recibo. O maior Dynamic Type foi exercitado no saldo e nas conferências. Uma execução adicional confirmou que o histórico apresenta a base original de R$ 900,00 e a corrigida de R$ 910,00 nesse tamanho. O script padrão do iOS passou em um terceiro simulador descartável dedicado, com os testes opt-in executados nas jornadas separadas.
- No iOS 27.2, o VoiceOver leu o saldo de R$ 837,00, a data da conferência, a divergência original de +R$ 45,00 e o resíduo de +R$ 55,00. A mudança de T0 e a resolução de um dia sem hora foram verificadas por HTTP e PostgreSQL. A jornada de correção no simulador usou o T0 original do S10.
- A amostra do log da API continha identificadores técnicos e rotas parametrizadas. A busca por marcadores fictícios, IDs financeiros, tokens e centavos da fixture teve zero ocorrências. As cópias de entradas e rótulos no log unificado do simulador estavam no subsistema de instrumentação do XCTest. Guarde essa distinção ao revisar logs de teste e diagnósticos da aplicação.
