# ADR-003: Navegação de próxima/anterior restrita ao contexto de setlist

## Status
Aceito

## Contexto
O teleprompter permite avançar/retornar entre músicas (RF-28, RF-29).
Esses controles pressupõem uma sequência definida. Quando uma música é
aberta diretamente pela busca global (RF-23), sem pertencer a nenhuma
setlist no momento, não existe uma ordem natural de "próxima" ou
"anterior", logo a música pode não ter nenhum vínculo com as demais.

## Decisão
Os controles de próxima/anterior ficam disponíveis apenas quando a
música foi aberta a partir de uma setlist (contexto com ordem definida).
Quando aberta via busca global, esses controles ficam ocultos ou
desabilitados (RF-30).

## Consequências

**Positivas:**
- Elimina ambiguidade de comportamento — navegação sequencial só existe
  onde uma sequência de fato existe.
- Implementação mais simples: a tela do teleprompter opera em dois modos
  claros ("modo setlist" com posição conhecida, ou "modo standalone"
  sem posição), sem precisar inventar uma ordem artificial para busca.

**Negativas:**
- Usuário não pode navegar sequencialmente pelos resultados de uma busca
  (por exemplo, passar de um resultado de busca para o próximo). Pode
  ser reavaliado em versão futura se o uso real indicar necessidade.
