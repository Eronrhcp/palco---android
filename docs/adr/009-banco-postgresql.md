# ADR-009: Banco de dados relacional — PostgreSQL

## Status
Aceito

## Contexto
O backend precisa de um banco de dados relacional para persistir
usuários, músicas, cifras, setlists e jobs de importação (ver modelo
ER). As alternativas consideradas foram PostgreSQL e MySQL, ambos
gratuitos, maduros e com bom suporte no ecossistema Spring Data JPA.

## Decisão
O banco de dados relacional do backend será PostgreSQL.

## Alternativas consideradas
- **MySQL:** também viável, com grande presença histórica no mercado,
  inclusive em sistemas bancários legados. Descartado em favor do
  PostgreSQL por dois motivos: (1) suporte mais rico a tipos de dado
  avançados e recursos de SQL (window functions, CTEs, JSONB), com maior
  valor de aprendizado; (2) tendência de mercado, mesmo empresas com
  núcleo legado em outro banco (Oracle/DB2) vêm adotando PostgreSQL para
  novos serviços/microsserviços.

## Consequências

**Positivas:**
- Acesso a recursos de SQL mais avançados para prática (window
  functions, CTEs, tipos JSONB), reforçando a skill de SQL para além do
  CRUD básico.
- Alinhamento com a tendência atual de adoção em backends novos no
  mercado de fintech/banking, mesmo quando o núcleo legado usa outro
  banco.
- Conformidade rigorosa com padrão SQL e ACID, reduzindo comportamentos
  implícitos inesperados durante o desenvolvimento.

**Negativas:**
- Nenhuma desvantagem relevante identificada para o contexto deste
  projeto (uso local/pessoal, sem exigência de alta concorrência).
