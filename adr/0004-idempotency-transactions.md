# ADR 0004 - Idempotency for Transactions

## Status

Accepted and implemented in the authenticated transaction API.

## Decision

Each logical `POST /transactions` operation uses a stable UUID `Idempotency-Key`, stable complete payload (including `clientMutationId`), deterministic request hashing, and an atomic PostgreSQL transaction. Idempotency keys are scoped by authenticated user.

Identical persisted requests replay the canonical response. A different payload with the same user and key returns `409 Conflict`.

Concurrency of the first write for a new key requires explicit test coverage and validation evidence.

The backend implementation and its concurrency evidence remain the source of truth for locking details and deployment status.

## Adendo · Escritas da conta MVP

O namespace permanece único por usuário, inclusive entre recursos antigos e novos. O hash da abertura MVP inclui versão de canonização, operação, escopo do titular autenticado e payload normalizado. Os hashes e recibos de transações existentes continuam legíveis.

Após autenticar e validar o envelope, adquirir o mesmo lock transacional por usuário e UUID normalizado. Um recibo compatível é reapresentado antes de verificar a revisão ou a existência de conta ativa. Um hash incompatível retorna 409. A espera por locks na operação MVP é limitada. Timeout, perda da resposta ou 5xx deixam o resultado incerto. A tentativa continua com a mesma chave e os mesmos dados. Nenhum TTL automático se aplica aos recibos do MVP.

Abrir a conta, confirmar a base e persistir a resposta canônica são uma única transação. A API responde após commit. Os demais comandos sobre a conta devem aplicar a fronteira do [ADR 0006](0006-mvp-account-revision-and-coverage.md).

O I1-B aplica o mesmo namespace ao registro manual. O hash v1 inclui operação, titular, conta, revisão esperada e dados enviados. O recibo devolve o ID do movimento e a revisão confirmada. Ele é reapresentado antes de consultar a revisão atual. O iOS grava o corpo HTTP já codificado e a chave antes do primeiro POST de abertura ou de registro. A confirmação ou a rejeição definitiva encerra a intenção. Uma resposta ambígua preserva a intenção para o mesmo titular e ambiente.
