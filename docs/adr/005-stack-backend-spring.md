# ADR-005: Stack do backend — Java com Spring Boot

## Status
Aceito

## Contexto
O backend próprio precisa de uma stack definida para implementar
autenticação, import/parsing de cifra e persistência. Alternativas
consideradas incluíam Node.js e Kotlin com Ktor, além de Java com Spring
Boot. 

## Decisão
O backend será construído em Java com Spring Boot.

## Consequências

**Positivas:**
- Aproveita e aprofunda a skill de Java já presente.
- Ecossistema maduro (Spring Security, Spring Data JPA) reduz esforço de
  implementação de autenticação e persistência, sem abrir mão de
  aprendizado real das práticas usadas em produção.

**Negativas:**
- Maior verbosidade e curva de configuração inicial comparado a
  frameworks mais leves (ex: Ktor, Express).
- Exige familiarização com convenções e "magia" do ecossistema Spring
  (injeção de dependência via anotações, ciclo de vida de beans) caso
  ainda não seja familiar.
