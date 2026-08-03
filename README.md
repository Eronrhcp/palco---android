# Palco

Teleprompter de cifras para músicos de voz e violão. Permite montar
repertório a partir de um link do Cifra Club, com controle de fonte,
tom e velocidade por música, leitura confiável mesmo sem internet no
momento da apresentação.

## Status

🚧 Em desenvolvimento — fase de design concluída, implementação em
andamento por vertical slices (ver Roadmap abaixo).

## Stack

- **App:** Android nativo, Kotlin, MVVM, Clean Architecture, Coroutines/Flow, RxJava, Hilt, Retrofit, Room
- **Backend:** Java, Spring Boot
- **Observabilidade:** Firebase (Crashlytics, Analytics)

## Documentação

Todo o processo de design está documentado e versionado antes da
implementação:

- [PRD (Product Requirements Document)](docs/prd.md)
- [Modelo de domínio (ER)](docs/diagrams/er-model.md)
- [Arquitetura do sistema (C4 — Contexto e Container)](docs/diagrams/c4-container.md)
- [Especificação da API (OpenAPI)](docs/api/openapi.yaml)
- [Architecture Decision Records (ADRs)](docs/adr/)

## Roadmap de implementação

Organizado por **vertical slice** — cada item entrega uma funcionalidade
completa, de ponta a ponta (banco de dados → backend → app), em vez de
construir uma camada inteira por vez.

- [ ] **0. Fundação** — scaffolding do projeto Android multi-módulo, projeto Spring Boot e provisionamento do banco de dados
- [ ] **1. Autenticação** — cadastro, login, refresh token, logout (RF-01 a RF-04)
- [ ] **2. Setlist básico** — criar, listar, renomear, excluir (RF-08, RF-12)
- [ ] **3. Importação de cifra assíncrona** — link/manual, job, polling (RF-05 a RF-07, ADR-006)
- [ ] **4. Motor de transposição + Teleprompter** — leitura, rolagem, navegação (RF-16 a RF-20, RF-27 a RF-30)
- [ ] **5. Repertório avançado** — adicionar/remover música, reordenar, config por música (RF-09 a RF-11, RF-13 a RF-15)
- [ ] **6. Busca** — dentro de setlist e em todo o repertório (RF-21 a RF-23)
- [ ] **7. Robustez offline** — testes dedicados de modo avião e cenários de falha (RNF-01 a RNF-03)
- [ ] **8. Observabilidade** — Crashlytics, Analytics, cache do backend (RNF-18, RNF-19)

## Sobre o projeto

Este projeto nasceu de uma necessidade real: a dificuldade de acessar
rapidamente uma cifra durante uma apresentação improvisada ("canja"),
sem depender de internet ou de navegar lentamente por um site de
cifras. Além de resolver esse problema, o projeto foi construído com um
processo de engenharia completo — desde levantamento de requisitos até
decisões arquiteturais documentadas — como forma de aplicar na prática
habilidades de design de sistemas, modelagem de dados e arquitetura de
software.