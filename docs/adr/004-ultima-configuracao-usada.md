# ADR-004: Última configuração usada por música, independente de setlist

## Status
Aceito

## Contexto
Fonte, tom e velocidade são configuráveis por combinação Setlist+Música
(RF-13), guardados em `SetlistMusica`. Quando uma música é aberta fora de
qualquer setlist (via busca global, RF-23), não existe uma combinação
Setlist+Música para consultar, mas a tela do teleprompter ainda precisa
de algum valor de fonte/tom/velocidade para exibir.

Três opções foram consideradas: (a) uma configuração padrão própria da
música, independente de qualquer setlist; (b) a última configuração
usada por aquela música, em qualquer contexto; (c) valores neutros fixos
a cada abertura.

## Decisão
A música guarda seu próprio "último estado usado" (`ultimaFonteUsada`,
`ultimoTomUsado`, `ultimaVelocidadeUsada`, em `Musica`), atualizado toda
vez que a música é tocada — seja dentro de uma setlist ou de forma
standalone. Ao abrir uma música fora de contexto de setlist, esses
valores são exibidos.

## Consequências

**Positivas:**
- Preserva a intenção mais recente do usuário; menor surpresa ao reabrir
  uma música em qualquer contexto.
- Evita consulta cara (percorrer todas as setlists que contêm a música
  para achar a mais recentemente usada), pois o valor já fica materializado
  direto em `Musica`.

**Negativas:**
- Introduz denormalização controlada: quando a música é tocada dentro de
  uma setlist, o mesmo valor de tom/fonte/velocidade é escrito tanto em
  `SetlistMusica` quanto em `Musica` (dual write). Isso exige disciplina
  de implementação: a escrita nos dois lugares deve ocorrer na mesma
  operação/use case, sob risco de os dois campos ficarem inconsistentes
  se esquecida em algum fluxo futuro.
