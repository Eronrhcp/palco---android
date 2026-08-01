# ADR-002: Fetch e parsing da cifra ocorrem no backend, não no app

## Status
Aceito

## Contexto
O usuário cola um link do Cifra Club no app; o sistema precisa buscar
aquela página e extrair (parsear) título, artista, tom original e corpo
da cifra. Essa lógica poderia rodar diretamente no app Android ou no
backend próprio.

O HTML do Cifra Club pode mudar sem aviso, quebrando o parser (risco
R-01), e requisições automatizadas podem sofrer bloqueio/rate limiting
(risco R-02).

## Decisão
O fetch da página e o parsing do HTML acontecem no backend. O app envia
apenas o link para o backend e recebe de volta os dados já estruturados
(JSON).

## Consequências

**Positivas:**
- Correção de um parser quebrado (R-01) é feita uma única vez no
  backend, sem depender de nova versão do app na Play Store.
- Permite cache compartilhado por URL entre diferentes usuários,
  reduzindo requisições repetidas ao Cifra Club (mitiga R-02).
- Retry com backoff exponencial é implementado uma única vez, de forma
  centralizada e testável.
- Reforça separação de responsabilidade entre cliente (app "burro" nesse
  ponto) e servidor (lógica de integração externa).

**Negativas:**
- Importar uma nova música passa a depender da disponibilidade do
  backend — não é possível fazer isso 100% offline (mitigado pelo
  fallback manual, RF-06, quando parsing falha).
- Centraliza responsabilidade de manutenção da integração externa no
  backend, exigindo monitoramento ativo (RNF-18).
