# I4 · Correções, invalidações e substituição de conferências

Referências de produto e implementação. [Baseline v0.2](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/4391010) · [Arquitetura](https://ghcferrari75.atlassian.net/wiki/spaces/Lenver/pages/5898244) · [ADR 0006](../adr/0006-mvp-account-revision-and-coverage.md) · [Entrega I3](i3-delivery.md)

## Contrato e persistência

| Operação | Comportamento |
| --- | --- |
| `POST /mvp/audit-previews` | Recebe conta, alvo, operação, motivo e termos novos. A leitura retorna a revisão, o saldo antes e depois, os cortes afetados e um token vinculado ao titular e aos termos. |
| `POST /mvp/audited-changes` | Recebe os mesmos termos, `expectedRevision`, `previewToken` e `Idempotency-Key`. Reapresenta primeiro o recibo compatível. Valida token e revisão sob lock para uma escrita nova. A resposta traz autor, motivo, instante, alvo e revisão. |
| `GET /mvp/account/{accountId}/history/{movementId}/versions` | Lista o fato original e as versões subsequentes acessíveis ao mesmo titular. O histórico identifica a ação vigente em `auditAction`. |
| `GET /mvp/account/{accountId}/checks` | A visão completa inclui as observações substituídas e os IDs da sucessão. Somente observações vigentes com resíduo diferente de zero ficam em `view=pending`. |

Corrigir cria uma versão com o mesmo ID de movimento, conta, moeda e tipo. A edição admite valor positivo em centavos, descrição e ocorrência UTC dentro da cobertura, com até seis casas de segundo na API. Omissão conserva o valor do campo e a precisão do fato, inclusive a indicação de dia sem hora. Descrição vazia remove a descrição. Invalidação guarda uma última versão auditável, retira o efeito financeiro e encerra a edição daquele movimento.

Cada vínculo do I3 fica associado ao ID estável. A atribuição compara a contribuição no corte pela versão vigente com a contribuição pela versão na revisão original da conferência. Uma despesa deslocada após o corte ou invalidada deixa de contribuir no cálculo vigente daquele corte. As cifras originais permanecem congeladas.

A substituição cria uma observação e uma ligação imutável à imediatamente anterior. O autor e o instante constam na nova observação, e o motivo consta no vínculo. Vínculos antigos permanecem auditáveis por meio da conferência em que foram registrados. A nova conferência inicia seu próprio cálculo e pode receber vínculos próprios.

O PostgreSQL trava a conta antes das dependências. A revisão, a trilha e o recibo são confirmados na mesma transação. Uma nova chave com revisão vencida requer nova prévia. O replay com a chave original devolve o mesmo recibo e o app relê o estado vigente. O token usa uma chave derivada do segredo de assinatura da instância. Rotação do segredo exige uma nova prévia para confirmações ainda inéditas.

## Migração

Aplicar `migrations/20261123_audit_mvp_corrections.sql` após a migração I3, antes de disponibilizar a API I4. Instalações limpas usam `db.sql`. Ambas as rotas preservam lançamentos, conferências, vínculos e recibos existentes. Manter a aplicação com papel sem DDL, TRUNCATE ou permissão de desativação dos triggers. Implantar a API I4 junto com o schema I4 para que todos os leitores MVP usem as versões vigentes.

## Aceite e reprodução

| Cenário | Verificação |
| --- | --- |
| S03 | Corrigir a padaria de R$ 25,00 para R$ 24,00 produz saldo de R$ 783,00 e resíduo de +R$ 1,00. As cifras originais continuam R$ 845,00 / R$ 800,00 / +R$ 45,00. A substituição preserva a observação original e a sua trilha. |
| S04 | Mover a padaria após o corte preserva R$ 782,00 no saldo e reabre +R$ 25,00 no corte. Invalidar a despesa de R$ 20,00 na ramificação independente produz R$ 802,00 e +R$ 20,00. |
| S09 | Com duas correções e uma conferência na mesma revisão, a vencedora avança uma vez e as demais recebem conflito. A leitura durante a transação vê a revisão anterior integralmente. Falha ao persistir o recibo reverte a versão ou conferência, o vínculo e a revisão. |

Execute `npm run build`, `npm test -- --runInBand`, `npm run test:e2e -- --runInBand` e `npm run test:integration` na API. A variável `TEST_DATABASE_URL` deve apontar exclusivamente para PostgreSQL 16 descartável com nome terminado em `_test`. As suítes de integração recriam o schema.

Para a jornada HTTPS, inicialize outro banco descartável com `db.sql` e confie uma CA de teste apenas nos simuladores dedicados. Configure `DATABASE_URL` em loopback, `I4_TLS_DIR` com `localhost.pem` e `localhost.key`, `I4_SEED=1` e credenciais JWT fictícias. Após compilar a API, execute `node test/fixtures/i4-https.cjs`. A fixture usa a porta 55456 e interrompe a primeira resposta de correção após o commit para verificar chave, corpo e recibo no replay.

No iOS, use o scheme `Fintech` com o runtime iOS 26.5 e `TEST_RUNNER_FINTECH_I4_HTTPS_TEST=1` para os métodos `testI4CorrectionRecoversAfterRelaunchOverHTTPS` e `testI4InvalidatesAndReplacesOverHTTPS` de `FintechUITests/MvpAccountIntegrationTests`. Após a correção, use iOS 27.2 com `TEST_RUNNER_FINTECH_I4_VOICEOVER_TEST=1` para `testI4VoiceOverReadsTheReopenedDifference`. Preserve a fixture entre esses testes e use `-testLanguage en -testRegion BR`. Uma nova execução completa começa com banco vazio.

O app permanece no opt-in Debug. A confirmação congela o corpo e a chave no armazenamento protegido, e a retomada valida ambiente, titular, conta e sessão. Correção da base inicial e adoção de contas legadas estão fora do I4.

## Verificação local de 23 de novembro de 2026

- API compilada. ESLint dos arquivos TypeScript alterados e validação das 166 referências internas do OpenAPI passaram. Passaram 161 testes unitários, 48 testes HTTP e 97 testes de integração no PostgreSQL 16.14. O caminho de upgrade foi exercitado com dados legados e com uma conta I3 que já tinha observação, despesa recuperada, vínculo e recibo.
- iOS com builds Debug para simulador e Release genérico. Passaram 121 testes Swift. No iOS 26.5, a API confirmou a correção antes de o proxy descartar a resposta. Após encerrar e reabrir o app, a fixture confirmou a mesma chave, os mesmos bytes do corpo e o mesmo recibo. Passaram a auditoria de Dynamic Type máximo e a jornada de invalidação e substituição.
- No iOS 27.2, o VoiceOver leu o saldo de R$ 783,00, o original de +R$ 45,00 e o resíduo de +R$ 1,00. As validações usaram apenas dados fictícios. A proteção de dados com dispositivo físico bloqueado permanece sem verificação.
