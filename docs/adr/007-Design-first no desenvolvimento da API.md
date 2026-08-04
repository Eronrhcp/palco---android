# ADR-007: Design-first no desenvolvimento da API

## Status
Aceito

## Contexto
Existem duas abordagens comuns para desenvolver uma API REST: code-first
(o código dos controllers é escrito primeiro, e a especificação
OpenAPI/Swagger é gerada automaticamente a partir dele, tipicamente via
springdoc-openapi) e design-first (a especificação OpenAPI é escrita e
acordada antes de qualquer código, servindo como contrato entre as
partes que a consomem).

O Palco tem duas partes que consomem a mesma API: o App Android e o
Backend desenvolvidas pela mesma pessoa, mas em momentos e ritmos
possivelmente diferentes. Todas as decisões de domínio (PRD, modelo ER,
ADRs) já haviam sido tomadas antes de qualquer linha de código, tornando
natural formalizar o contrato de API nessa mesma fase de design.

## Decisão
A API do Palco segue a abordagem design-first: a especificação OpenAPI
(`docs/api/openapi.yaml`) foi escrita manualmente antes da implementação
de qualquer controller, servindo como fonte da verdade do contrato entre
App Android e Backend.

## Alternativas consideradas
- **Code-first com springdoc-openapi:** mais rápido para manter
  sincronizado automaticamente com o código, mas inverte a ordem desejada, o contrato nasceria como reflexo da implementação, em vez de guiá-la.
  Descartado para a fase inicial, podendo ser reavaliado para manter a
  documentação sincronizada ao longo da evolução do projeto após o MVP.

## Consequências

**Positivas:**
- Permite desenvolver App Android e Backend de forma desacoplada, usando
  o contrato já definido como acordo prévio (inclusive com respostas
  mockadas no app antes do backend estar pronto).
- O contrato reflete diretamente as decisões já registradas no PRD, ER e
  ADRs, mantendo rastreabilidade (cada rota comentada com o RF de
  origem).
- Erros de modelagem de contrato (ex: regra de negócio mal expressa) são
  detectados e corrigidos antes de existir código para refatorar.

**Negativas:**
- Risco de o contrato e a implementação divergirem ao longo do tempo,
  caso o YAML não seja mantido atualizado manualmente conforme o backend
  evolui, exige disciplina de revisão em toda mudança de rota.
- Especificação escrita manualmente não passou por validação automática
  de sintaxe/schema no momento da criação; deve ser validada (ex:
  Redocly CLI ou Swagger Editor) antes de ser tratada como definitiva.