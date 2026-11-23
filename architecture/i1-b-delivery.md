# I1-B · Registro, histórico e recuperação de envios

O incremento liga o formulário SwiftUI à conta pessoal BRL confirmada no I1-A. A pessoa registra receitas e despesas com hora de ocorrência e consulta o saldo e os movimentos confirmados. A abertura e o registro podem ser retomados após perda de resposta ou encerramento do app. A área integrada continua no opt-in Debug do I1-A.

Referências de escopo e aceite. [Arquitetura](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/5898244) · [Requisitos v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/4391010) · [Decisões aprovadas](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/98326) · [ADR 0004](../adr/0004-idempotency-transactions.md) · [ADR 0006](../adr/0006-mvp-account-revision-and-coverage.md)

## Contrato

- `POST /mvp/transactions` exige Bearer, `Idempotency-Key` UUID, conta própria ativa BRL, centavos inteiros de 1 a 999.999.999, tipo, instante com fuso, `clientMutationId` e revisão esperada. A descrição é opcional. O recibo 201 contém ID, ocorrência, instante de gravação, centavos em string e a nova revisão. Um replay compatível devolve o recibo original mesmo depois que a revisão avança.
- `GET /mvp/account/{accountId}/history` devolve o resumo e a primeira página na mesma consulta PostgreSQL. A ordem é por ocorrência e ID, ambos decrescentes. O cursor mantém o corte da primeira página e o serviço devolve `STALE_REVISION` se os dados mudarem. O cliente recarrega o resumo e a primeira página nesse caso. Uma conta legada própria pode exibir histórico com saldo indisponível e sem base confirmada.
- Conta alheia ou ausente devolve 404. Revisão obsoleta, base ausente e chave incompatível têm códigos 409 distintos. Datas fora da cobertura, datas futuras e envelopes inválidos retornam 400. Timeout, perda de resposta, falha temporária ou expiração da sessão preservam a intenção no dispositivo.
- `POST /transactions` conserva o DTO, o recibo e a canonização legados. Contas ativas do MVP rejeitam novos efeitos desse escritor. Esse bloqueio também abrange binários antigos que escrevem no mesmo schema. A adoção permanece bloqueada.

O iOS grava uma intenção por ambiente e titular, com conta ou escopo de abertura, operação, chave e bytes JSON. A gravação atômica usa Application Support com proteção completa e exclusão de backup. A geração da sessão é verificada antes e depois da rede. A recuperação consulta primeiro a identidade autenticada e, em registros, exige a mesma conta ativa. O recibo confirmado encerra a intenção e é seguido por leitura atual da API. A introdução de uma operação de registro com a conta atual impede que a mesma intenção pendente ganhe outra chave.

## Migração

Em um banco com as migrações existentes e o I1-A aplicados, use `20261123_enable_mvp_transaction_registration.sql`. Em instalação limpa, use `db.sql`. A migração adiciona marcadores de revisão e recibo, índices e vínculo adiado ao recibo. Ela substitui a função do trigger existente para admitir apenas o INSERT revisionado, jamais UPDATE ou DELETE na conta MVP. O avanço da revisão e a validação de cobertura ocorrem sob lock da conta. Se faltar o recibo correspondente no commit, o PostgreSQL desfaz o efeito e o avanço da revisão. Movimentos e recibos legados mantêm seus dados e chaves. Use um papel de execução sem DDL, TRUNCATE e permissões de desativação de triggers.

## Reprodução em ambiente isolado

Execute na API `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e `TEST_DATABASE_URL=<URL_DO_BANCO_DESCARTÁVEL_TEST> npm run test:integration`. A integração recria o schema. O caminho de upgrade usa fixture legada anterior ao I1-A e as duas migrações em ordem.

Para o cenário iOS, use um segundo banco PostgreSQL 16 descartável, inicializado por `db.sql`. Providencie uma CA de teste confiável apenas no simulador dedicado e os arquivos `localhost.pem` e `localhost.key` para o proxy. Configure `JWT_ACCESS_SECRET`, `JWT_ISSUER` e `JWT_AUDIENCE` com valores fictícios e inicie o runner a partir da API.

```sh
DATABASE_URL='postgresql://<USUARIO_LOCAL>@127.0.0.1:<PORTA>/fintech_i1b_https_test' \
I1B_TLS_DIR='<DIRETORIO_TLS_TESTE>' \
node test/fixtures/i1b-https.cjs
```

Crie `i1b-a@example.test` e `i1b-b@example.test` com a senha fictícia usada pelo teste. Deixe A sem conta e crie para B uma conta legada chamada `Conta antiga fictícia`. Reinicie o runner e use novas fixtures para cada repetição. O proxy loopback repassa o HTTPS para Nest e encerra a conexão depois do 201 do segundo lançamento. O teste libera o retorno do recibo quando termina a verificação da identidade B. Os logs do proxy registram se chave, corpo e recibo coincidem, sem registrar seus valores.

No iOS, execute no destino dedicado abaixo. O comando não apaga o simulador nem usa o teste de produção.

```sh
TEST_RUNNER_FINTECH_I1B_HTTPS_TEST=1 xcodebuild \
  -project Fintech.xcodeproj -scheme Fintech \
  -destination 'platform=iOS Simulator,id=<UDID_DEDICADO>' \
  -parallel-testing-enabled NO \
  -only-testing:FintechUITests/MvpAccountIntegrationTests/testI1BRegistersAndRecoversCommittedSendAcrossRelaunchOverHTTPS \
  test
```

O simulador verifica gravação, reabertura e exclusão de backup. A confirmação da classe de proteção e do comportamento com aparelho bloqueado exige um iPhone físico com código de acesso.

## Evidência de 23/11/2026

- A API passou no build, no lint dos arquivos alterados, em 157 testes unitários, em 37 testes HTTP e em 73 testes da suíte com PostgreSQL 16.14. A suíte cobriu upgrade, data em T0 e um microssegundo antes, data futura, isolamento, paginação, revisão obsoleta, replay concorrente e falha ao gravar o recibo antes do commit.
- O build Release para o alvo genérico iOS passou. No iPhone 17 simulado com iOS 26.5, passaram 116 testes Swift e nove testes de interface, incluindo a jornada HTTPS opt-in. O teste de iPad, a integração independente do I1-A e o smoke de produção foram pulados.
- No fluxo HTTPS, A abriu a conta com R$ 900,00, registrou a despesa e consultou R$ 845,00. O proxy descartou a resposta da receita depois do commit. O app exibiu a pendência, foi encerrado e reaberto, e manteve a operação isolada durante o login de B. A nova entrada de A recuperou a confirmação e exibiu saldo de R$ 855,00 e dois lançamentos após nova reabertura. O proxy comparou a chave, os bytes do corpo e o recibo JSON. O PostgreSQL ficou na revisão 3 com dois movimentos, dois recibos de movimento e um de abertura.
- Foram inspecionados os estados de envio pendente, saldo indisponível sem base, confirmação e histórico. A interface em Dynamic Type máximo passou nas auditorias XCTest de tamanho de fonte e descrição de elementos. A auditoria de logs da API e do proxy encontrou os IDs técnicos de requisição e nenhum marcador de credencial, descrição ou ID financeiro da fixture. VoiceOver manual e Data Protection com o iPhone bloqueado seguem pendentes em aparelho físico.
