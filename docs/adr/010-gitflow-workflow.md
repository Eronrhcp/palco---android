# ADR-010: Adoção de Git Flow como workflow de versionamento

## Status
Aceito

## Contexto
Até este ponto, commits eram feitos diretamente na branch `main`, sem
branches de feature nem Pull Requests, aceitável na fase de scaffolding
(Slice 0), mas arriscado a partir do momento em que código de domínio
real passa a ser desenvolvido incrementalmente (Slice 1 em diante).

Dois modelos de workflow foram considerados: GitHub Flow (mais simples,
apenas `main` + branches de feature, comum em times com deploy
contínuo) e Git Flow (`main` + `develop` + `feature/*` + `release/*` +
`hotfix/*`, historicamente mais associado a ciclos de release discretos).

O App Android tem um ciclo de publicação por natureza discreto (build,
teste, geração de artefato, submissão à Play Store), diferente de um
serviço com deploy contínuo, o que se alinha melhor ao conceito de
branch de release do Git Flow.

## Decisão
Adotar Git Flow em ambos os repositórios (`palco-app` e `palco-api`):
`main` (produção/release, protegida), `develop` (integração), branches
`feature/*` (por funcionalidade, nascem de `develop`), `release/*`
(estabilização pré-publicação) e `hotfix/*` (correção urgente, nascem de
`main`). Ambos os repositórios passam a exigir Pull Request antes de
qualquer merge em `main` ou `develop` (branch protection no GitHub).

Este workflow entra em vigor a partir deste ponto, commits anteriores
feitos diretamente em `main` não são retroativamente reorganizados.

## Alternativas consideradas
- **GitHub Flow:** mais simples, adequado a deploy contínuo. Descartado
  como modelo único porque o ciclo de release do App Android é discreto
  por natureza, favorecendo o conceito de branch de release do Git Flow;
  manter o mesmo modelo nos dois repositórios também simplifica o
  hábito de trabalho entre eles.

## Consequências

**Positivas:**
- Reduz risco de quebrar `main`/`develop` com código não revisado.
- Cria hábito de revisão do próprio diff antes do merge (mesmo em
  projeto solo, sem revisor externo), prática valiosa por si só.
- Histórico de Pull Requests fica documentado no GitHub, servindo como
  evidência adicional de processo para quem avaliar o repositório.
- Alinhamento com competência citada explicitamente em vagas-alvo do
  mercado (versionamento e estratégias de branching).

**Negativas:**
- Mais overhead de processo que GitHub Flow, sem benefício de deploy
  contínuo automatizado ainda configurado (CI/CD não implementado nesta
  fase).
- Exige disciplina para não relaxar de volta a commits diretos em
  `main`/`develop`, já que não há um segundo revisor externo forçando a
  regra.
