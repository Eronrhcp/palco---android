# ADR-006: Processamento assíncrono da importação de cifra

## Status
Aceito

## Contexto
O RNF-07 já prevê indicador de progresso para importações acima de 2
segundos, o que tornaria uma resposta síncrona suficiente para as
necessidades reais do MVP — o volume de uso esperado (um único usuário)
não impõe uma exigência técnica de processamento assíncrono.

Entretanto, existe um objetivo de aprendizado paralelo ao projeto:
praticar concorrência tanto no backend (Java) quanto no app (Kotlin).
Um fluxo assíncrono de importação cria essa oportunidade de forma
natural, sem ser artificial — o app já precisa, de qualquer forma, lidar
com uma operação de rede que pode demorar.

## Decisão
A importação de cifra (`POST /cifras/importar`) passa a responder
imediatamente com `202 Accepted` e um identificador de job
(`ImportacaoJob`), processando o fetch e o parsing em background no
backend. O app consulta o status do job (polling) até que seja concluído
ou falhe.

No backend, o processamento em background usa mecanismos de concorrência
do Java/Spring (`CompletableFuture` e/ou `@Async`). No app, o polling é
implementado com Coroutines/Flow, permitindo observar o status sem
bloquear a UI.

## Alternativas consideradas
- **Processamento síncrono:** mais simples, suficiente para a escala
  real do MVP (apoiado no RNF-07). Descartado porque não oferece a
  mesma oportunidade de prática de concorrência nos dois lados do
  sistema, que é um objetivo explícito deste projeto.

## Consequências

**Positivas:**
- Prática real de concorrência no backend (`CompletableFuture`/`@Async`
  em Java) e de Coroutines/Flow no app (polling de status).
- Aproxima o projeto de um padrão comum em sistemas de produção para
  operações potencialmente lentas ou instáveis (integração com serviço
  externo não confiável, como o Cifra Club).

**Negativas:**
- Introduz complexidade não exigida pela escala real de uso do produto
  — decisão motivada por aprendizado, não por necessidade.
- Adiciona uma nova entidade ao domínio (`ImportacaoJob`), exigindo
  atualização do modelo ER já fechado.
- Exige revisão do contrato OpenAPI já definido: `POST /cifras/importar`
  muda de resposta síncrona (`201`) para assíncrona (`202` + job), e
  precisa de um novo endpoint de consulta de status.
- App precisa implementar lógica de polling (intervalo de consulta,
  timeout, tratamento de job que nunca conclui).
