# I1-A · Conta pessoal BRL, base e saldo

A pessoa autenticada consulta a conta pessoal do MVP ou abre uma conta BRL nova. Ela informa o saldo anterior ao início da cobertura, revisa os termos devolvidos pela API e confirma a abertura. A API grava conta, base e recibo na mesma transação. A consulta devolve saldo, revisão e instante de referência. Uma conta própria antiga pode ser consultada por seleção explícita. Sem base confirmada, a API devolve `base_required` com `balanceMinor: null`.

As decisões constam no [ADR 0006](../adr/0006-mvp-account-revision-and-coverage.md) e no [adendo ao ADR 0004](../adr/0004-idempotency-transactions.md). A [arquitetura proposta](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/5898244), os [requisitos v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/4391010) e as [decisões de produto](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/98326) orientam o recorte.

## Contrato e ativação

- `GET /mvp/account` consulta a seleção ativa do titular. Sem seleção, lista contas BRL próprias para consulta. `GET /mvp/account/{accountId}` consulta uma conta própria explicitamente. Conta alheia e inexistente retornam 404.
- `POST /mvp/account-previews` valida os termos sem persistir efeitos. `POST /mvp/account` confirma uma abertura e exige `Idempotency-Key` UUID. O resultado 201 canônico é gravado no namespace de recibos existente. 409 distingue conflito de chave, conta ativa e adoção bloqueada. O saldo e a revisão são strings decimais exatas.
- A migração `20261123_add_mvp_account_base.sql` requer schema com propriedade de contas e recibos e as migrações anteriores aplicadas. Ela acrescenta tabela, índice e trigger. Em instalação limpa, `db.sql` já contém esses objetos. Testar cada caminho separadamente.
- Antes de expor as rotas, aplicar a migração e verificar que os escritores usam o mesmo banco, com trigger ativo e papel de execução sem DDL, TRUNCATE ou capacidade de desabilitar triggers. A adoção de conta existente segue bloqueada até auditoria da população, do schema e dos consumidores. Bancos pré-ownership populados exigem plano separado.
- O caminho novo do iOS é habilitado em Debug com `-staging -mvp-account` e `FINTECH_STAGING_BASE_URL` apontando para HTTPS isolado. As credenciais desse caminho são separadas por URL. O formulário de movimentos anterior permanece no caminho existente.
- Em resultado incerto, o iOS mantém a chave e os termos da abertura em memória para repetir na mesma sessão. Após reinício, uma nova leitura recupera a conta caso o servidor tenha concluído o commit. A recuperação persistente da intenção permanece para I1-B.

## Verificação reproduzível

Em `fintech-api`, rode `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e `TEST_DATABASE_URL=<URL_DO_BANCO_DESCARTÁVEL_TEST> npm run test:integration`. A suíte de integração recria o schema e exige que o banco termine em `_test`. O teste I1-A semeia conta, movimento e recibo antes de aplicar a migração.

Para o iOS/API, use um segundo banco descartável com `db.sql` e sirva a API em `https://localhost:55440` com uma CA de teste confiável somente no simulador dedicado. Cadastre `i1a-a@example.test` e `i1a-b@example.test` com a senha fictícia `i1a disposable password`. Deixe A sem conta e crie para B uma conta BRL pela rota anterior `POST /accounts` com nome `Conta antiga fictícia`. Execute no diretório do projeto iOS com `<UDID_DESCARTÁVEL>` substituído pelo simulador criado para o teste.

```sh
TEST_RUNNER_FINTECH_I1A_HTTPS_TEST=1 xcodebuild \
  -project Fintech.xcodeproj -scheme Fintech \
  -destination 'platform=iOS Simulator,id=<UDID_DESCARTÁVEL>' \
  -parallel-testing-enabled NO \
  -only-testing:FintechUITests/MvpAccountIntegrationTests test
```

Use novas fixtures antes de repetir o cenário de primeira abertura. O script `scripts/verify.sh` apaga o simulador que selecionar. Use o destino dedicado explicitamente.

## Evidência de 23/11/2026

- O build da API, 157 testes unitários e 37 testes HTTP passaram. No PostgreSQL 16.14 descartável, passaram 68 testes. O cenário de upgrade comparou as linhas legadas e repetiu o POST antigo com seu recibo original. A leitura incluiu um lançamento exatamente em T0, excluiu o anterior por um microssegundo e o futuro, e retornou valores exatos acima da faixa segura de um número JSON.
- O build e a suíte padrão do iOS passaram no iPhone 17 simulado com iOS 26.5. Swift Testing reportou 112 testes. Na suíte de interface padrão, 8 casos passaram. O cenário de iPad e a integração opt-in foram pulados nessa execução. O smoke de produção permaneceu desabilitado.
- O teste HTTPS opt-in passou separadamente. A entrou, revisou e confirmou R$ 900,25 e consultou o saldo. Depois da saída e da entrada de B, o app exibiu uma conta legada própria e saldo indisponível. O banco registrou uma conta ativa e um recibo de abertura para A. A verificação usou a API Nest real, certificados locais de teste e apenas fixtures fictícias.
- A adoção legada, a implantação e a validação manual em iPhone físico, incluindo VoiceOver, continuam pendentes.

## Próximo incremento

I1-B integra o escritor de movimentações com revisão e cobertura, histórico paginado e a retomada persistente de intenções no iOS. Conferências, retroativos de recuperação e correções financeiras pertencem a incrementos posteriores.
