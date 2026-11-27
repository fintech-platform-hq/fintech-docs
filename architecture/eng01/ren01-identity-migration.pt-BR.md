# REN01 — Migração de identidade e fechamento documental

**Data:** 26/11/2026\
**Estado:** alterações locais e registros de aceite publicados; aguardando revisão e aceite da REN01 pelo responsável.\
**Escopo:** consolidar a identidade Lenver em componentes ativos, regularizar ENG01 v1.0, V0 e V0.1 aprovados, corrigir os drafts da Roadmap e do Checklist Mestre, e registrar exceções. Não inicia V1.

## Resultado local

| Ambiente | Alteração ou estado verificado | Limite preservado |
| --- | --- | --- |
| iOS/Xcode | Projeto, diretórios, arquivo de entitlements, app e targets de teste usam `Lenver`; scheme `Lenver`; imports Swift e documentação de desenvolvimento atualizados. `FINTECH_STAGING_BASE_URL` passou a `LENVER_STAGING_API_BASE_URL` nos testes que iniciam o app. | Bundle IDs, serviços Keychain, host de produção, credenciais e ícone atual do target não mudaram. O ícone Inflexão permanece referência até aceite visual definitivo. |
| Backend | Pacote npm, lockfile raiz, resposta de identidade da API, teste e exemplo correspondente usam `lenver-api`. OpenAPI já se identifica como Lenver. | `JWT_ISSUER`, `JWT_AUDIENCE`, `APPLE_CLIENT_ID`, rotas, payloads e banco `fintech` foram preservados por compatibilidade ou por contrato. |
| Docker | Dockerfile usa Node 24 e `npm ci`; `.dockerignore` reflete Node 24. O dry-run offline do npm confirmou compatibilidade entre package.json e lockfile v3. | Compose mantém serviços `postgres`/`api`, contêineres `fintech-postgres`/`fintech-api`, banco `fintech` e volume `postgres_data`. O daemon Docker não está acessível nesta sessão; nomes de projeto, imagem, rede, volumes e contêineres existentes não foram inventariados. |
| Documentação ativa | READMEs, instruções Xcode e pacote ENG01 atualizados. Um único registro reúne mapeamento, exceções, serviços externos e ações manuais. Os registros Confluence de ENG01, Roadmap e Checklist foram regularizados e publicados. | Handover, entregas I1–I5, comandos aprovados, fixtures e evidências históricas mantêm sua nomenclatura original, identificados como históricos. |
| GitHub | Repositórios remotos existentes já usam `lenver-hq/lenver-ios`, `lenver-hq/lenver-backend` e `lenver-hq/lenver-docs`; consultados em leitura. | Nenhum repositório ou branch remoto foi renomeado. Alterações locais seguem sem stage, commit ou push. |

## Registro único de exceções técnicas e históricas

| Identificador preservado | Motivo e risco de renomear | Condição para reavaliar |
| --- | --- | --- |
| Bundle IDs `com.ferrari.Fintech`, `com.ferrari.FintechTests`, `com.ferrari.FintechUITests` e variantes `.staging` | Identidade de assinatura, instalação e compatibilidade Apple. O Bundle ID de StagingRelease é fixo: `com.ferrari.Fintech.staging`. | Plano autorizado para App IDs, capabilities, provisioning profiles, distribuição e convivência das instalações. |
| Serviços Keychain `com.ferrari.Fintech.authentication` e `com.ferrari.Fintech.staging.authentication` | Renomear o serviço pode separar a aplicação das credenciais já gravadas. O namespace de staging acrescenta o endpoint completo e varia por endpoint; o Bundle ID de staging não varia. | Plano de migração que preserve leitura/transferência segura das credenciais e valide produção e staging. |
| `APPLE_CLIENT_ID=com.ferrari.Fintech` | Client ID é identidade de confiança usada no fluxo Apple e pode estar vinculado a Service ID/capabilities. | Inventário somente leitura no Apple Developer e plano coordenado com backend e Apple. |
| `JWT_ISSUER` e `JWT_AUDIENCE` atuais | Claims assinadas usadas para validar tokens; mudar a identidade pode invalidar sessões existentes ou permitir incompatibilidade entre ambientes. | Plano de rotação/aceitação de claims com janela e compatibilidade explícitas. |
| Host público `https://fintech-api-87yw.onrender.com` e services Render `fintech-api` / `fintech-api-staging` | Host, service slug e certificados podem ser consumidos por clientes, callbacks, scripts e URLs de Keychain. O inventário confirma configuração Render, mas não prova liveness nem propriedade de DNS alternativo. | Mudança individual autorizada com plano de endpoint, clientes, callbacks, TLS e retorno. Nenhum serviço foi renomeado ou implantado nesta entrega. |
| Compose: serviços `postgres`/`api`, contêineres `fintech-*`, banco `fintech`, volume `postgres_data` e rede de projeto implícita | Names podem ser consumidos por scripts; projeto/volume pode apontar para dados persistentes. Como o Docker daemon está inacessível, não foi possível provar quais recursos locais existem nem sua origem. | Inventário local read-only do Docker e mapeamento explícito de contêiner, imagem, rede e volume antes de qualquer mudança. Nenhum volume, contêiner ou imagem foi removido. |
| Flags de suites históricas `FINTECH_I*_HTTPS_TEST` e comandos I1–I5 | Entradas documentadas em evidências e runbooks históricos; renomear pode tornar reproduções aprovadas inválidas. | Revisão individual dos runbooks e adaptação coordenada dos consumidores, preservando os comandos históricos. |
| Manifesto, hashes, ZIPs, fixtures e texto histórico com `Fintech` | São evidências aprovadas ou snapshots que registram o estado testado e suas referências originais. Alterá-los prejudicaria proveniência e verificação de hashes. | Nunca editar em lugar; publicar adendo versionado com evidência nova. |
| Ícone Inflexão | O guia visual ainda o classifica como provisório. O usuário determinou mantê-lo como referência, sem ativá-lo no target. | Aceite visual definitivo separado. |

Usos de `fintech` como categoria de mercado, nomes de fixtures fictícias e identificadores de compatibilidade não foram substituídos por busca/substituição global.

## Inventário externo somente em leitura

| Serviço | Resultado | Classificação |
| --- | --- | --- |
| GitHub | Integração respondeu para os três repositórios Lenver; públicos, branch principal `main`, não arquivados. | **Verificado em leitura** para nome e metadados consultados; sem alteração remota. |
| Confluence | Drafts de ENG01 (`12713988`), Roadmap (`688129`) e Checklist (`9699329`) lidos e publicados com o aceite e as correções REN01. | **Verificado e regularizado** para as páginas atualizadas; outros gates continuam abertos. |
| Figma | Arquivo oficial `cNCeTerlVDKWSHz9lFwnQo` e página Brand Guide/Inflexão identificados em leitura. | **Verificado como referência**; aceite visual final do ícone permanece pendente e o target não foi alterado. |
| Render | Workspace contém `fintech-api` (`https://fintech-api-87yw.onrender.com`) e `fintech-api-staging` (`https://fintech-api-staging.onrender.com`), ambos ligados a `lenver-hq/lenver-backend`, branch `main`, auto-deploy desligado e serviço não suspenso. Nenhum Render Postgres foi listado. | **Configuração verificada em leitura**; saúde HTTP, variáveis/segredos e equivalência entre host público e serviço não foram verificados. Sem deploy ou mudança de slug. |
| Neon | Após a conexão aparecer como disponível, as chamadas de leitura `list_projects`, `search` e `list_organizations` retornaram `Tool … not found` no servidor MCP. Nenhum projeto, branch, endpoint ou banco foi confirmado. | **Ferramentas expostas no catálogo, execução indisponível no servidor** nesta sessão; não inferir presença nem ausência de recursos. |
| Apple Developer | Não há integração de conta Apple Developer disponível nesta sessão. | **Não acessível**; Bundle IDs/capabilities/perfis remotos permanecem pendentes de revisão manual. |
| Docker local | Cliente Docker 29.5.3 presente; acesso ao daemon/socket foi negado. | **Inventário operacional não verificado**. A evidência V0.1 anterior registra Docker Engine 29.5.3 e imagem Node `v24.22.3`; ela antecede a mudança REN01 para `npm ci`. |

O endpoint público `/health` não pôde ser validado pela ferramenta web. A listagem Render é evidência de configuração da conta, não prova de resposta HTTP, banco saudável ou implantação correspondente.

## Adequação das skills

Swift/Xcode tem cobertura instalada suficiente para esta migração de identidade e para orientação de testes (`ios-spec-delivery`, `swift-testing-expert`, `swiftui-ui-patterns` e `swift-security-expert`). Documentação tem suporte por `confluence` e `domain-modeling`. Não foi identificada skill dedicada a NestJS/PostgreSQL ou infraestrutura Docker entre as instaladas; a busca `find-skills` encontrou alternativa orientada a Docker, mas nenhuma instalação foi feita sem autorização. Para o escopo desta entrega, os comandos e fontes oficiais do projeto cobriram as verificações executadas. Nenhuma skill foi instalada ou alterada.

## Regularização e critérios do Checklist

ENG01 v1.0, V0 e V0.1 foram aceitos pelo responsável em 26/11/2026. O manifesto V0.1 e seus hashes permanecem imutáveis. As alterações REN01 atualizam o código e registram que o Dockerfile agora usa `npm ci`; a compatibilidade package/lockfile passou em `npm ci --dry-run --ignore-scripts --offline --no-audit --no-fund`, mas o novo build e runtime Docker continuam pendentes por falta de acesso ao daemon.

Critérios documentais atendidos no registro REN01: PD-009, MD-009, MD-010, AR-007, AR-010, AR-012, AR-014, DQ-010 e DQ-011. O Checklist v18 totaliza 67 concluídas, 27 parciais, 4 não confirmadas e 22 pendentes. RQ-008 conserva seu aceite anterior. MD-011 permanece parcial por faltar cobertura explícita revisada do conjunto de sequências de conflito/recuperação; AR-011 e AR-015 preservam lacunas de responsáveis; AR-016 preserva localização/cópias externas por confirmar. PD-010/011/012/013, DQ-012/013/018, UX-015/018 e COB-002 mantêm seus estados e evidências próprias. A regularização documental não equivale a implementação nem validação operacional.

## Verificações desta execução

- `xcodebuild -list -project Lenver.xcodeproj`: confirmou projeto, targets `Lenver`, `LenverTests`, `LenverUITests` e scheme `Lenver`.
- Build genérico `StagingRelease` para iOS: passou; avisou que o ramo MVP não é executável nessa configuração, como já definido para o target.
- `build-for-testing` genérico do Simulator: passou para app e alvos de teste.
- Teste unitário no Simulator: **não executado**. CoreSimulator desconectou e o destino isolado deixou de ser encontrado; nenhuma limpeza/erase de simulador foi executada.
- Backend: build passou; teste unitário focal 5/5 e e2e focal 13/13 passaram.
- `npm ci` dry-run offline: passou sem reinstalar dependências. `bash -n scripts/verify-compatibility.sh` passou.
- `git diff --check` nos três repositórios: passou. Nenhuma operação Git de escrita foi realizada.
- O harness V0.1 completo não foi repetido: a sessão não consegue acessar Docker; a evidência aceita anterior continua separada da mudança `npm ci`.

## Ações manuais remanescentes

1. **Docker local — somente inventário:** inicie/conecte o Docker Desktop e execute, no diretório `/Users/ferrari/Projects/lenver/lenver-backend`, `docker ps -a --no-trunc`, `docker compose ls`, `docker image ls --digests`, `docker network ls` e `docker volume ls`. Registre quais recursos pertencem ao projeto e quais scripts dependem dos nomes atuais. Não execute `compose down`, `rm`, `prune` ou `volume rm`. Só então decida, em uma autorização separada, se os nomes `fintech-*` podem mudar.
2. **Neon:** abra a organização/projeto correto no console Neon e consulte projects, branches, endpoints, databases, roles e região sem abrir/copiar connection strings. Registre IDs/status e referências de integração; não altere, suspenda nem migre recursos.
3. **Render:** no dashboard, compare read-only o serviço, branch/commit implantado e domínio dos dois serviços; valide o `GET /health` de cada host por uma rede com acesso. Não revele valores de variáveis nem use ações de deploy, rename ou configuração.
4. **Apple Developer:** em Certificates, Identifiers & Profiles, confira App IDs, capability Sign in with Apple, Services IDs associados, perfis e grupos Keychain para os Bundle IDs da tabela acima. Registre somente identificadores e estado, sem copiar chaves ou segredos. Uma proposta de renomeação exige plano separado de assinatura, instalação, Keychain e sessões.
5. **Visual:** manter o ícone Inflexão sem ativação até o aceite visual definitivo.

## Processo

Execução por um agente, sem subagentes. Duas intervenções do responsável: manter o ícone Inflexão pendente de aceite visual e consultar as integrações instaladas exclusivamente em leitura. Retrabalho observado: nomes de contêiner Compose foram restaurados após o daemon inacessível impedir a inspeção das dependências; a primeira forma Markdown do update Confluence foi rejeitada no dry-run por conter Smart Links/HTML e foi refeita em HTML preservando os elementos. O teste XCTest no Simulator não iniciou porque CoreSimulator desconectou. O modelo expõe apenas a família GPT-6; variante e effort não estão disponíveis à sessão, portanto o piloto GPT-6 Luna Max não pode ser confirmado. Duração observável entre o checkpoint instrumentado em 26/11/2026 14:33:24 UTC e o registro final às 15:37:08 UTC: 1 h 03 min 44 s. Consumo de tokens não foi disponibilizado.
