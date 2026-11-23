# ADR 0006 · Revisão, cobertura e escrita da conta MVP

## Status

Decisão do incremento I1-A, derivada de D1–D3 e da especificação v0.2.

## Decisão

- Manter a conta em `accounts` e associar uma única conta pessoal BRL por titular em `mvp_accounts`. A chave composta garante titularidade e moeda. I1-A abre uma conta nova mediante confirmação explícita. A conta, a base confirmada e a revisão inicial 1 aparecem juntas. Uma conta própria antiga continua elegível para consulta com estado de base ausente. Adoção permanece bloqueada até verificar ownership, população e escritores.
- A base registra centavos assinados, início da cobertura T0 em UTC, zona IANA de referência, titular, instante do servidor e revisão inicial. O saldo é B0 mais entradas menos despesas com T0 ≤ ocorrência ≤ C, em que C é o instante da consulta. Ausência de base tem valor nulo. As comparações e a soma exata ocorrem no PostgreSQL em uma única consulta com a revisão e o corte. Os recursos novos transportam centavos e revisão em strings decimais.
- Cada escrita admitida que afete a conta financeira avança a revisão monotônica na mesma transação do efeito, da autoria e do recibo. Os locks seguem a ordem da chave idempotente, do titular quando há disputa de abertura, da conta e das dependências. A abertura usa a linha do titular e a chave única. Comandos futuros sobre conta existente devem bloquear a linha da conta e verificar a revisão esperada sob lock.
- No I1-A, o banco rejeita INSERT, UPDATE e DELETE de lançamentos da conta ativa. O POST legado mantém o replay anterior à escrita e rejeita novos efeitos nessas contas. A proteção também vale para instâncias antigas que usem o mesmo schema e SQL comum. Adoção e habilitação de um escritor exigem protocolo equivalente, proteção contra bypass e cobertura transacional. A aplicação deve usar um papel sem privilégios de DDL, TRUNCATE ou desativação de triggers em implantação.
- Contas, lançamentos e recibos do schema com ownership permanecem intactos no upgrade. A migração anterior de ownership não é caminho de atualização para bancos pré-ownership populados. Esse caso exige plano auditado separado.

## Verificação por requisito

| Critério | Implementação e evidência esperada |
| --- | --- |
| F01 · Propriedade | AuthGuard e filtros pelo principal; dois usuários por HTTP e teste de isolamento da geração no cliente |
| F02 · Conta e base | Prévia pura e confirmação transacional; PostgreSQL para bases assinadas, disputa, replay e upgrade |
| F04 · Saldo | Consulta única com limites inclusivos; PostgreSQL para ausência, zero, fronteiras e precisão |
| Q02/Q03 · Integridade | Recibo canônico, exclusão mútua e bloqueio do escritor legado; integração PostgreSQL e teste de reenvio em memória no iOS |
| Jornada iOS | Autenticação existente, formulário nativo de confirmação e leitura com estados explícitos; build, testes e fluxo HTTPS isolado |

I1-B contempla lançamento compatível com revisão e cobertura, histórico integrado e recuperação persistente da intenção no iOS. A habilitação legada permanece condicionada à auditoria mesmo após I1-B.
