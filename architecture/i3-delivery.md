# I3 · Recuperação retroativa e correspondência

A pessoa abre uma conferência com divergência, informa valor e dia, revisa possíveis despesas e escolhe vincular um lançamento, confirmar uma despesa distinta ou adiar. A descrição ajuda a ordenar as sugestões. A escolha pelo histórico continua disponível quando o valor ou a data informados não encontram o lançamento correto. Uma única sugestão também exige confirmação.

[Arquitetura](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/5898244) · [Baseline v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/4391010) · [Decisões D1–D3](https://ghcferrari75.atlassian.net/wiki/spaces/Fintech/pages/98326) · [Entrega I2](i2-delivery.md)

## Contrato

| Operação | Garantia |
| --- | --- |
| `POST /mvp/recovery-previews` | Recebe conta, conferência, centavos positivos em string, dia civil e hora precisa opcional em UTC. A prévia retorna a revisão, os candidatos elegíveis da própria conta, as cifras no corte e as projeções separadas para uma nova despesa. A prévia é somente leitura. |
| `POST /mvp/recoveries` | Recebe os dados revisados, `expectedRevision`, evidência escrita e chave idempotente UUID. `link` exige a seleção explícita de um movimento. `create` exige confirmação própria e grava a despesa e o vínculo de forma atômica. O recibo identifica autor, conferência, vínculo, movimento, revisão confirmada e instante de gravação. |
| `GET /mvp/account/{accountId}/checks` | Mantém as cifras e o estado binário do I2. Acrescenta `resolutionStatus` com `open`, `partially_explained`, `coincident` ou `resolved`. A opção de pendências filtra pelo resíduo diferente de zero. |
| `GET /mvp/account/{accountId}/history` | Mantém os campos do I1-B. Quando `occurrenceDay` está presente, o app mostra o dia e informa que a hora é desconhecida. O campo legado `occurredAt` carrega o limite inferior do intervalo para ordenação e paginação desse registro. |

O vínculo sozinho tem efeito monetário zero. Seu efeito explicativo é o delta entre a contribuição do movimento no cálculo vigente e a contribuição no cálculo original. Por exemplo, vincular uma despesa que já fazia parte do saldo original explica zero centavos. Cada movimento conta uma vez por conferência. Resolução exige resíduo zero, original diferente de zero e deltas vinculados que expliquem integralmente a divergência original. Zero sem atribuição suficiente é coincidência numérica. Uma redução parcial exige o mesmo sinal do original, módulo menor e contribuição vinculada na direção da redução. Uma inversão continua aberta.

A referência externa é opcional e precisa vir da origem declarada. A unicidade é por conta, origem e identificador. A mesma referência e o mesmo destino recuperam o recibo já gravado, inclusive com outra chave. Referência incompatível retorna `EXTERNAL_REFERENCE_CONFLICT`. O hash interno de idempotência compara o conteúdo da requisição. A referência externa é armazenada separadamente. Sem referência, uma revisão vencida exige nova prévia.

## Tempo e cortes

O dia civil usa o fuso IANA da conta. A API calcula o começo do dia e o começo do seguinte. O intervalo inclusivo termina um microssegundo antes do próximo dia, limitado ao relógio do servidor. O PostgreSQL congela os dois limites ao registrar a despesa. T0 e os cortes existentes precisam incluir ou excluir o dia inteiro. O limite exato é inclusivo. Se a hora puder mudar o resultado, a prévia informa que é necessária. O iOS deixa a hora em branco, rejeita horários inexistentes e pede o deslocamento UTC quando uma hora civil se repete.

Uma conferência posterior com corte dentro do intervalo de uma despesa sem hora retorna `OCCURRENCE_TIME_REQUIRED`. Escolher outro corte é possível no I3. Alterar a precisão de uma movimentação já confirmada pertence ao fluxo de correções. Clientes que consomem o histórico devem usar `occurrenceDay` ao apresentá-lo.

## Migração e consistência

Aplique `migrations/20261123_recover_mvp_expenses.sql` depois da migração de conferências I2 e antes de executar a API I3. Uma instalação limpa usa `db.sql`. As novas colunas ficam nulas em lançamentos anteriores. O vínculo é imutável e sua trilha preserva declaração, evidência, autor, conferência, movimento e revisão. Chaves compostas e verificações de banco preservam a titularidade. O banco exige o vínculo e o próprio recibo no commit de uma despesa recuperada.

A transação verifica primeiro o recibo por titular e chave. Em seguida trava a conta, verifica a revisão e faz a escrita. Nova despesa, vínculo, revisão e recibo avançam juntos. A mesma conta usa esse lock para registrar transações e conferências. O iOS congela corpo e chave no armazenamento protegido antes do envio e verifica ambiente, titular, conta e sessão ao retomar. Adiar fecha a decisão sem escrever no servidor.

## Cenários de aceite

| Cenário | Resultado esperado |
| --- | --- |
| S03 | Saldo atual de R$ 827,00 para R$ 802,00 e depois R$ 782,00. Resíduo +R$ 45,00 → +R$ 20,00 → R$ 0,00. Original R$ 845,00 calculado e R$ 800,00 informado, com diferença de +R$ 45,00. |
| S04, trecho I3 | R$ 50,00 retroativos antes do corte levam a resíduo de −R$ 5,00 e pendência aberta. |
| S05 | Prévia, adiamento e vínculo preservam o saldo. Uma compra distinta entra uma vez. Referência disputada com outro destino e prévia obsoleta exigem revisão. |
| S07–S09, trechos I3 | Reenvio usa o corpo e a chave originais. Troca de titular isola a intenção. Falha antes do vínculo reverte movimento e revisão. Escritas concorrentes usam uma única revisão confirmada. |
| S11 | Aceitar dia integral inequívoco, exigir hora quando o dia cruza T0 ou corte e preservar a precisão em dias de 23 e 25 horas. |

## Reprodução

Na API, execute `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e `npm run test:integration`. Use `TEST_DATABASE_URL` apontando exclusivamente para PostgreSQL 16 descartável com nome terminado em `_test`. Os testes recriam o schema. Para a jornada, use outro banco descartável, inicializado com `db.sql`, e um certificado para `localhost` com a CA de testes confiada apenas nos simuladores dedicados.

```sh
DATABASE_URL='postgresql://<USUARIO>@127.0.0.1:<PORTA>/fintech_i3_https_test' \
I3_TLS_DIR='<DIRETORIO_COM_LOCALHOST_PEM_E_KEY>' I3_SEED=1 \
JWT_ACCESS_SECRET='<SEGREDO_FICTICIO_DE_TESTE>' \
JWT_ISSUER='fintech-i3-fixture' JWT_AUDIENCE='fintech-i3-fixture' \
node test/fixtures/i3-https.cjs
```

O proxy usa a porta 55448 em loopback. A fixture semeia titulares fictícios, injeta uma falha de prévia e interrompe a primeira resposta de nova despesa após o commit. Para repetir a jornada, use um banco vazio e reinicie a fixture. Conserve a fixture após a jornada para a verificação de VoiceOver.

```sh
TEST_RUNNER_FINTECH_I3_HTTPS_TEST=1 xcodebuild -project Fintech.xcodeproj -scheme Fintech \
  -destination 'platform=iOS Simulator,id=<UDID_26_5>' -parallel-testing-enabled NO \
  -only-testing:FintechTests \
  -only-testing:FintechUITests/MvpAccountIntegrationTests/testI3ReviewsLinksDefersAndRecoversConfirmedExpenseOverHTTPS \
  -testLanguage en -testRegion BR test

TEST_RUNNER_FINTECH_I3_VOICEOVER_TEST=1 xcodebuild -project Fintech.xcodeproj -scheme Fintech \
  -destination 'platform=iOS Simulator,id=<UDID_27_2>' -parallel-testing-enabled NO \
  -only-testing:FintechUITests/MvpAccountIntegrationTests/testI3VoiceOverReadsResolutionAndPreservedDifference \
  -testLanguage en -testRegion BR test
```

As jornadas usam o opt-in Debug existente. A evidência de armazenamento protegido em um iPhone físico bloqueado exige teste em aparelho. Correções, invalidações, substituição de conferências e revisão da base pertencem a I4/I5.
