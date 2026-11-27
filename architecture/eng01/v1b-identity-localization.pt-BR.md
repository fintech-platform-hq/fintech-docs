# V1-B · Recibos de identidade e idiomas do iOS

Atualização técnica local de 27/11/2026. As condições de homologação da ENG01, os aceites do Checklist Mestre e o fluxo de autorização de publicação continuam valendo.

## Recibos de vínculo e desvínculo

A OpenAPI 1.1.0 aceita o cabeçalho opcional `Idempotency-Key` com UUID em `POST /v1/auth/apple/link`, `/apple/unlink`, `/password/link/confirm` e `/password/unlink` sob o prefixo `/v1/auth`. O passo `POST /v1/auth/password/link` inicia a prova por e-mail. O vínculo ocorre na confirmação dessa prova.

O backend insere o recibo de sucesso na mesma transação do efeito, sob a trava do titular. A chave é titular, família da sessão e UUID. O recibo registra o tipo de operação e dispensa reapresentar a prova após o sucesso. Um UUID associado a outro tipo no mesmo escopo devolve `409`. Cada nova intenção requer UUID novo. Se o primeiro resultado persistido for reapresentado, o backend devolve aquele resultado original. O recibo não guarda senha, código, prova, identidade Apple nem corpo do pedido.

`GET /v1/auth/method-operations/{operationId}` exige bearer válido daquela família. Retorna `committed` com a ação registrada, ou `unknown` quando o escopo consultado não tem recibo visível. `unknown` permanece inconclusivo durante a execução, após uma recusa que não gerou recibo ou quando outra família consulta a mesma chave. A consulta não reapresenta o comando. O desvínculo Apple com UUID exige sessão de senha verificada. Isso preserva a família iniciadora para consultar o resultado. O caminho antigo sem cabeçalho conserva seu contrato; a UI V1 orienta a entrar por senha para remover Apple.

O iOS grava o UUID e a ação antes do envio, em armazenamento protegido e excluído de backup, isolado por ambiente e família. O app restaura a pendência apenas após validar a sessão com a API. Enquanto não houver recibo compatível, a área de métodos permite consultar o resultado e suspende novas mudanças de método. Logout, troca de família ou invalidação retiram a pendência da interface. O identificador local é uma referência de consulta e o backend decide a autoridade pelo bearer. Em caso de confirmação por recibo, o estado agregado dos métodos é recarregado separadamente, pois pode ter mudado desde o commit original.

## Idioma e moeda no iOS

- Inglês é o idioma de desenvolvimento do projeto, fonte do catálogo e fallback do bundle. O identificador `en` serve texto em inglês americano, com formatação `en_US`.
- Português do Brasil usa os recursos `pt-BR` e formatação `pt_BR`. O iOS escolhe entre os recursos instalados pelas preferências de idioma do sistema ou por app. A tela de Preferências encaminha aos Ajustes do sistema.
- Região e idioma não escolhem a moeda. Os valores permanecem denominados em BRL e a API continua usando centavos inteiros. Os formatadores exibem separadores segundo a localização ativa.
- Alterações no catálogo devem cobrir `en` e `pt-BR`. Verifique `developmentRegion`, `knownRegions`, o catálogo e os recursos compilados. Exercite um terceiro idioma no simulador e mantenha a checagem de BRL com a região desse idioma. [CFBundleDevelopmentRegion, Apple](https://developer.apple.com/documentation/bundleresources/information-property-list/cfbundledevelopmentregion)

## Composição e ordem de integração

`StagingRelease` seleciona o bundle `com.ferrari.lenver.staging`, um Keychain escopado ao endpoint HTTPS fornecido durante o build e a área autenticada de identidade. A tela de conta financeira é uma composição Debug opcional. A Home e o registro financeiro da V2 aguardam seu próprio recorte. `Release` mantém a composição de registro legado com seu destino de produção. A mudança de App ID e Keychain do candidato REN04 exige verificação de continuidade antes de distribuir qualquer build.

O bootstrap espera `20261127_v1_method_receipts`. Um banco de V0 exige, em ordem, `20261127_identity_session.sql` e `20261127_identity_method_receipts.sql`. Um banco que já tem o primeiro marcador V1 aplica somente a segunda migration. Preserve as migrations já aplicadas. Em um clone descartável, valide o catálogo e as permissões de `SELECT` e `INSERT` na tabela de recibos para o papel da API e o acesso de leitura ao marcador; a criação da tabela e a mudança de marcador pertencem ao migrador. Integre o contrato/API antes de expor o cliente V1-B. Como a readiness compara o marcador exato, planeje a transição e o retorno do par binário/schema junto com o responsável pelo ambiente. Eles dependem de uma janela autorizada.
