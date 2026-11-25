# I2 · Conferência datada e retomada

A pessoa informa o saldo que observou e a data e hora da observação. O app consulta o cálculo da API no mesmo corte, mostra o sinal da diferença e pede confirmação. Ao confirmar, a pessoa pode registrar uma despesa pelo formulário existente. A tela **Conferências e pendências** permite consultar todas as observações ou só as divergências em aberto, inclusive depois de reabrir o app.

[Arquitetura](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/5898244) · [Baseline v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/4391010) · [Decisões aprovadas](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/98326) · [Entrega I1-B](i1-b-delivery.md)

## Contrato e invariantes

| Operação | Contrato |
| --- | --- |
| `POST /mvp/check-previews` | Recebe `accountId`, `observedMinor` como string de centavos Int64 assinados e `referenceAt` em UTC com até seis casas fracionárias. Devolve o cálculo, a diferença assinada e a revisão consultada. A prévia é somente leitura. |
| `POST /mvp/checks` | Recebe os mesmos campos, `expectedRevision` da prévia e `Idempotency-Key` UUID. Confirma a observação e devolve o recibo original. Um replay compatível reapresenta o 201 mesmo após nova revisão. |
| `GET /mvp/account/{accountId}/checks` | Retorna o resumo atual, até 50 observações e `nextAfter` em uma leitura PostgreSQL. `view=pending` limita a divergências em aberto. A página seguinte exige `after` e `revision` da página anterior. Ordem decrescente por corte, gravação e UUID. |

- A cobertura inclui o início da base e o corte. A API usa o relógio do servidor para rejeitar datas futuras. A zona IANA da conta contextualiza a escolha e a apresentação no iOS, enquanto o contrato recebe um instante UTC.
- A diferença é o saldo calculado menos o informado. A observação preserva o cálculo e a diferença originais. A lista também calcula o saldo e a diferença restantes no mesmo corte com os lançamentos visíveis na revisão consultada. Resíduo zero significa coincidência numérica. Cada nova conferência é independente.
- A consulta e a escrita usam o principal autenticado e a conta BRL própria com base confirmada. Conta alheia ou ausente retorna 404. Base ausente, revisão obsoleta e chave incompatível são conflitos distintos. Uma falha temporária permite reenviar a mesma intenção.
- A gravação avança uma única revisão por conta. A revisão usada na prévia é a revisão anterior à gravada no recibo. A observação, a revisão e o recibo são atômicos. A observação tem efeito monetário zero. O lock da chave por titular antecede o lock da conta, compartilhado com o registro do I1-B. A API lê o recibo antes de revalidar a revisão de uma escrita repetida.
- O iOS reaproveita a intenção protegida do I1-B. A conta e o principal precisam coincidir na retomada. O app congela corpo e chave antes do primeiro envio e recarrega a leitura canônica após o recibo. A rejeição definitiva por revisão vencida libera a intenção e pede nova prévia com os dados do formulário.

## Migração

Na instalação limpa, use `db.sql`. Em banco com I1-B, aplique `migrations/20261123_record_mvp_balance_checks.sql` após `20261123_enable_mvp_transaction_registration.sql` e antes de implantar a API I2. A migração adiciona a função de saldo por corte, a tabela de observações e o índice de paginação. O trigger congela a autoria, a referência à base, o instante de gravação, o cálculo e a diferença. O PostgreSQL rejeita alterações ou exclusões de observações e exige o recibo da própria gravação antes do commit. A função comum de saldo usa soma exata em centavos e as chaves estrangeiras preservam titularidade. As rotas existentes e os registros I1-B são preservados.

## Rastreabilidade da baseline

| Critério | Entrega e verificação |
| --- | --- |
| F03 · Registro manual | A confirmação dá acesso imediato ao formulário I1-B. A API mantém as validações de tipo, centavos, moeda e ocorrência. O teste integrado registra o café e recupera o ID confirmado. |
| F04 · Histórico e saldo | O saldo no histórico continua na mesma revisão dos movimentos. A consulta de conferências devolve o próprio resumo coerente com os recálculos no corte. Os indicadores de lançamento corrigido ou invalidado pertencem ao I4. |
| F05 · Conferência e retomada | Prévia revisionada, observações imutáveis, lista completa e pendências. A substituição auditada pertence à correção de observações em I4. |
| Q01 · Precisão e atomicidade | PostgreSQL 16 cobre limites de microssegundo, valor acima do Int64, corte inclusivo, disputa contra lançamento, falha no recibo e leituras diante de uma escrita em curso. |
| Q02 · Reenvio | Testes de API e Swift cobrem replay, conflito de chave, revisão vencida, titular diferente e a expiração da sessão. A fixture HTTPS interrompe a entrega de dois recibos após commit e compara chave, corpo e recibo no replay. |
| Q03 · Auditoria e diagnóstico | A observação preserva ID, conta, autor, referência à base, corte, horário de gravação, revisão e cifras originais. A API registra código HTTP, rota sem parâmetros e ID técnico de requisição. |
| Q04 · Clareza e acessibilidade | Formulário e navegação nativos. As telas rotulam valor informado, saldo calculado, diferença, horário e estado. O XCTest do fluxo HTTPS verifica falhas acionáveis e Dynamic Type máximo. No iOS 27.2, a navegação real do VoiceOver anunciou o horário, o estado e as cifras com o sinal. |
| S02 · Conferir e continuar | Original de R$ 845,00 / R$ 800,00 / +R$ 45,00. O café de R$ 18,00 reduz o saldo atual a R$ 827,00 e mantém o resíduo de +R$ 45,00. Na ramificação PostgreSQL, R$ 1,00 no corte reduz o resíduo a +R$ 44,00. Uma nova conferência coincidente preserva a anterior em aberto. |

## Reprodução

Execute `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e a suíte de integração com `TEST_DATABASE_URL` apontando exclusivamente para um PostgreSQL 16 descartável cujo nome termina em `_test`. A suíte recria o schema. Para HTTPS, reserve um segundo banco descartável, carregue o schema limpo e inicie a fixture com certificados locais `localhost.pem` e `localhost.key` e valores fictícios para `JWT_ACCESS_SECRET`, `JWT_ISSUER` e `JWT_AUDIENCE`. Instale a CA local apenas nos simuladores dedicados.

```sh
DATABASE_URL='postgresql://<USUARIO>@127.0.0.1:<PORTA>/fintech_i2_https_test' \
I2_TLS_DIR='<DIRETORIO_TLS_TESTE>' node test/fixtures/i2-https.cjs
```

Cadastre `i2-a@example.test` e `i2-b@example.test` com a senha fictícia usada no XCTest. Abra uma conta MVP para A com R$ 900,00 e uma data de cobertura anterior à despesa inicial. Registre uma despesa de R$ 55,00 anterior ao dia do teste e deixe B sem conta. A fixture encaminha as requisições HTTPS de `localhost:55445` ao Nest em loopback, injeta indisponibilidade de prévia e lista, simula 401 no acesso e na renovação e interrompe a entrega dos recibos já confirmados da conferência e do café.

```sh
TEST_RUNNER_FINTECH_I2_HTTPS_TEST=1 xcodebuild -project Fintech.xcodeproj -scheme Fintech \
  -destination 'platform=iOS Simulator,id=<UDID_DEDICADO>' -parallel-testing-enabled NO \
  -only-testing:FintechUITests/MvpAccountIntegrationTests/testI2ChecksAndContinuesAfterInterruptedSendsOverHTTPS \
  -testLanguage en -testRegion BR test
```

Para repetir o teste HTTPS completo, use novas fixtures e reinicie o proxy para limpar as falhas simuladas. Para testar a fala de VoiceOver, conserve a fixture com o café confirmado e rode o teste `testI2VoiceOverReadsTheCutAndSeparateBalances` com `TEST_RUNNER_FINTECH_I2_VOICEOVER_TEST=1`, Xcode 27 e simulador iOS 27 ou posterior. O teste anexa as falas do VoiceOver ao resultado XCTest.

## Evidência de 23/11/2026

- API. Build e lint passaram. As suítes passaram com 159 testes unitários, 47 testes HTTP e 81 testes em PostgreSQL 16.14. A verificação do log HTTPS correlacionou um sucesso e uma rejeição por IDs técnicos e verificou a ausência dos marcadores fictícios de credencial, e-mail, valores, descrição e IDs financeiros.
- iOS. Build de simulador e Release genérico passaram. A regressão executou 118 testes Swift com sucesso. Dos 13 XCTest de interface dessa execução, sete passaram, cinco foram pulados e um falhou. O teste legado de valor inválido encontra dois elementos com o mesmo identificador. O erro foi reproduzido em uma cópia limpa do I1-B no mesmo simulador.
- Jornada isolada. O iPhone 17 Pro simulado com iOS 26.5 recuperou os dois recibos com o mesmo corpo e chave após a interrupção. A fixture simulou expiração entre o primeiro envio e a retomada, e o teste verificou o isolamento durante o login com outro titular. O iPhone 18 Pro simulado com iOS 27.2 navegou a lista com VoiceOver e registrou a fala no resultado XCTest.

Validação em iPhone físico e verificação da proteção com aparelho bloqueado seguem separadas da evidência em simulador. O fluxo continua no opt-in Debug do I1. Recuperação retroativa, vínculos, correções e ajustes compensatórios ficam nos incrementos posteriores.
