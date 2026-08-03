# Arquitetura do Sistema (C4) — Palco

> Fase: design de arquitetura de sistema (Nível 1 e 2 do Modelo C4).
> Disciplina correspondente: Arquitetura de Software / Projeto de Sistemas.
>
> Diferença em relação ao diagrama de camadas mostrado anteriormente na
> fase de ideação: aquele era um diagrama de Componente (Nível 3 do C4),
> mostrando as camadas *dentro* do container "App Android". Aqui estamos
> um nível acima: mostrando os containers como unidades independentes.

## Nível 1 — Diagrama de Contexto

Visão externa do sistema: quem usa e com quais sistemas externos o Palco
se comunica, sem nenhum detalhe interno.

```mermaid
C4Context
    title Diagrama de Contexto - Palco

    Person(musico, "Músico", "Usuário do app, faz voz e violão")
    System(palco, "Palco", "Permite montar repertório e ler cifras em performance, com transposição de tom e leitura offline")
    System_Ext(cifraclub, "Cifra Club", "Site de cifras — fonte de dados via link colado pelo usuário")
    System_Ext(firebase, "Firebase", "Crashlytics e Analytics")

    Rel(musico, palco, "Usa para montar repertório e ler cifras em performance")
    Rel(palco, cifraclub, "Busca e extrai cifra a partir de um link (fetch on-demand)")
    Rel(palco, firebase, "Envia eventos de crash e uso")
```

## Nível 2 — Diagrama de Container

Abre o sistema Palco e mostra as unidades independentes (containers) que o
compõem no MVP, e como elas se comunicam.

```mermaid
C4Container
    title Diagrama de Container - Palco (MVP)

    Person(musico, "Músico", "Usuário do app")

    System_Boundary(palco, "Palco") {
        Container(app, "App Android", "Kotlin, MVVM, Clean Architecture", "Interface, teleprompter, transposição de tom, navegação de setlist")
        ContainerDb(room, "Banco local (Room)", "SQLite", "Persistência offline de setlists, músicas e cifras")
        Container(api, "Backend API", "Java, Spring Boot", "Autenticação (JWT), import e parsing de cifra, cache por URL")
        ContainerDb(db, "Banco de dados do backend", "a definir (relacional)", "Usuários e cache de cifras já parseadas")
    }

    System_Ext(cifraclub, "Cifra Club", "Fonte de dados de cifra")
    System_Ext(firebase, "Firebase", "Crashlytics e Analytics")

    Rel(musico, app, "Usa")
    Rel(app, room, "Lê e grava dados locais")
    Rel(app, api, "HTTPS/JSON — login, import de cifra")
    Rel(api, db, "Lê e grava")
    Rel(api, cifraclub, "HTTP GET — busca da página para parsing")
    Rel(app, firebase, "Envia eventos de crash e uso")
```

## Notas de decisão

- O **Backend API** será construído em **Java com Spring Boot** — decisão
  alinhada ao mercado de fintech/banking (Itaú, PagBank, Cielo), reforçando
  a skill de backend Java já presente no perfil. O banco de dados
  relacional específico (PostgreSQL, MySQL) ainda será definido na fase de
  design técnico detalhado.
- O **App Android** se comunica com o **Cifra Club** apenas indiretamente,
  através do Backend API — reforça a decisão já registrada de que o
  parsing acontece no backend (ver Anexo A do PRD, decisão 2).
- O container **Room** existe dentro do boundary do Palco mas fisicamente
  vive embarcado no próprio App Android — está separado no diagrama para
  deixar explícito que é uma unidade de persistência distinta da API.
