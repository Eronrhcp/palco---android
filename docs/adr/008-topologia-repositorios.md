# ADR-008: Topologia de repositórios e localização da documentação

## Status
Aceito

## Contexto
Optou-se por multi-repo (repositórios separados para app e backend), em
vez de monorepo, decisão mais alinhada ao mercado real, onde mobile e
backend costumam ter ciclos de vida e times independentes.

Parte da documentação de design já produzida (PRD, modelo ER, C4)
descreve o sistema como um todo, não apenas uma das partes, e precisa de
um local definido para não ser duplicada nem perdida entre os dois
repositórios. Outros artefatos (alguns ADRs, o OpenAPI) já são
claramente específicos de um lado só.

## Decisão
Dois repositórios: `palco-app` (Android) e `palco-api` (Spring Boot).

A documentação cross-cutting (PRD, modelo ER, diagramas C4) vive em
`palco-app`, por ser o repositório que representa o produto como um
todo. Cada repositório mantém seus próprios ADRs específicos:

- `palco-app`: ADR-003 (navegação restrita a setlist), ADR-004 (última
  configuração usada por música)
- `palco-api`: ADR-001 (autenticação própria), ADR-002 (parsing no
  backend), ADR-005 (stack Spring Boot), ADR-006 (importação
  assíncrona), ADR-007 (API design-first), ADR-008 (este documento)
- A especificação OpenAPI (`openapi.yaml`) vive em `palco-api`, por
  descrever o contrato que o backend implementa.

O README de `palco-api` linka de volta para a documentação cross-cutting
em `palco-app`.

## Alternativas consideradas
- **Terceiro repositório `palco-docs`**, contendo apenas documentação,
  sem código. Descartado por adicionar overhead de sincronização (mais
  um lugar a lembrar de atualizar) desproporcional ao tamanho do
  projeto, e por diluir a identidade do produto — um visitante buscaria
  o app primeiro, não um repositório de documentação isolado.

## Consequências

**Positivas:**
- Evita duplicação de documentação cross-cutting entre os dois
  repositórios de código.
- `palco-app` concentra a identidade do produto, sendo o ponto de
  entrada natural para quem for avaliar o projeto.
- Cada repositório mantém apenas os ADRs relevantes ao seu próprio
  contexto, evitando ruído.

**Negativas:**
- `palco-api`, isoladamente, não conta a história completa do sistema
  sem seguir o link para `palco-app`, quem avaliar apenas o backend
  precisa desse passo extra.
- Exige manter o link de referência entre os repositórios atualizado
  caso um deles seja renomeado ou movido.
