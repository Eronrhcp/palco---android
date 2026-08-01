# PRD — Palco (MVP)

> Status: Fase de design (pré-implementação)
> Última atualização: decisão de mover parsing de cifra para o backend

## 1. Visão geral / Problema

Músicos que fazem shows de voz e violão precisam ler cifras (letra + acordes) enquanto tocam e cantam. Hoje isso é feito com papel impresso, tablets com PDF estático, ou apps genéricos de cifra que não foram pensados pra uso em palco: fonte pequena, sem controle de rolagem automática, sem adaptação de tom por música, e — o mais crítico — dependentes de internet no momento da apresentação.

O problema fica ainda mais evidente em situações informais e imprevisíveis: em uma festa, ao saberem que o músico toca violão, é comum pedirem uma "canja" — uma música na hora, sem preparo prévio. Nesse momento, recorrer a um site de cifras genérico significa perder tempo navegando entre uma música e outra, o que esfria a plateia e quebra o clima. Para quem faz voz e violão, especialmente com músicas novas ou difíceis de memorizar, ter um apoio confiável e rápido de usar — tanto no palco planejado quanto na canja de improviso — faz diferença real na experiência, tanto do músico quanto de quem assiste.

O Palco existe para resolver esse problema: permitir acesso rápido ao repertório (planejado ou não), com controle total de fonte, tom e velocidade por música, e funcionamento confiável mesmo sem internet no momento da apresentação.

## 2. Objetivo do MVP

Este MVP existe para provar que um músico consegue, a partir de um link do Cifra Club, montar rapidamente uma lista de cifras pronta para leitura em performance — com controle de fonte, tom e velocidade por música — e acessá-la de forma confiável mesmo sem conexão à internet no momento da apresentação, sem necessariamente ter organização avançada de repertório, sincronização em nuvem, ou gestão de shows/local.

## 3. Público-alvo

**Usuário primário:** músico de voz e violão que se apresenta em bares, festas, eventos informais ou pequenos shows — solo ou em dupla.

**Contexto de uso:** em pé ou sentado, tocando e cantando simultaneamente, muitas vezes em ambiente com pouca luz e distância do celular/tablet maior que a leitura normal de tela. Uso acontece tanto em situação planejada (repertório definido antes do show) quanto em situação de improviso (pedido de música na hora, "canja").

**Alternativas atuais:** papel impresso, PDF estático em tablet, ou busca direta em site de cifras — todas lentas para trocar de música ou dependentes de internet no momento crítico.

## 4. Escopo

### Dentro do MVP

| Item | Por quê |
|---|---|
| Cadastro/login com e-mail e senha (autenticação própria no backend) | Necessário pra separar dados por usuário desde o início; decisão de treinar backend a sério (hash de senha, JWT, refresh token) |
| Colar link do Cifra Club → backend faz fetch + parser → extrai a cifra | Núcleo da hipótese do MVP |
| Fallback manual (colar cifra à mão se o parser falhar) | Sem isso, uma falha de parsing torna o app inutilizável — resiliência básica, não V2 |
| Criar listas, adicionar/remover músicas, ordenar músicas na lista | Organização mínima pra "repertório pronto pra leitura" fazer sentido |
| Configuração de fonte, tom, velocidade por combinação Lista+Música | Requisito explícito desde a ideia original |
| Transposição de tom (motor de cálculo) | Parte central da leitura em performance |
| Teleprompter com rolagem automática, play/pause, reiniciar, próxima/anterior | O produto em si |
| Busca de música (dentro de uma lista e em todas as listas) | Resolve diretamente a dor da "canja" (Seção 1) |
| Última configuração usada por música (fonte/tom/velocidade), independente da lista | Preserva a intenção do usuário ao abrir música fora de contexto de lista |
| Funcionamento offline após a música ter sido importada uma vez | Requisito não-negociável ("não pode falhar no palco") |
| Persistência local (Room) | Suporte técnico ao requisito acima |

### Fora do MVP (registrado de propósito)

| Item | Por quê fica de fora agora |
|---|---|
| Login social (Google) | Autenticação própria já é escopo suficiente pro MVP; entra em V2 |
| Sincronização em nuvem de dados do usuário (multi-dispositivo) | Excluído explicitamente no objetivo do MVP |
| Configurações gerais avançadas da lista (ritmo, metadados extras) | Ordenação e nome básico já bastam pra provar a hipótese |
| Gestão de locais de show + mapa | V3 — não é sobre ler cifra, é sobre gestão de agenda |
| Calendário de shows e controle de cachê | V3 — mesmo motivo acima |
| Dashboard financeiro (módulo Flutter) | Depende do item anterior existir primeiro |
| Compartilhamento de listas entre usuários | Feature nova, não validada ainda |
| Repertório "coringa" de músicas fáceis para canja | Refinamento sobre a busca já existente — avaliar após uso real do MVP |

## 5. Requisitos Funcionais

### Autenticação

| ID | Requisito |
|---|---|
| RF-01 | O sistema deve permitir que o usuário se cadastre com e-mail e senha, armazenando a senha com hash (bcrypt ou Argon2), nunca em texto puro. |
| RF-02 | O sistema deve permitir que o usuário faça login, retornando um access token (JWT) e um refresh token. |
| RF-03 | O sistema deve permitir renovar o access token expirado usando o refresh token, sem exigir novo login. |
| RF-04 | O sistema deve permitir que o usuário faça logout, invalidando o refresh token correspondente. |

### Importação de cifra

| ID | Requisito |
|---|---|
| RF-05 | O sistema deve permitir que o usuário cole um link do Cifra Club no app; o backend deve buscar a página e extrair (parsear) título, artista, tom original e corpo da cifra, retornando os dados estruturados para o app. |
| RF-06 | Caso o backend não consiga extrair a cifra automaticamente (falha de parsing), o sistema deve permitir que o usuário insira manualmente o texto da cifra, sendo esta salva localmente no dispositivo, sem depender de nova tentativa de parsing. |
| RF-07 | O sistema deve permitir que, no momento da importação, o usuário associe a música a uma lista existente ou crie uma nova lista. |

### Repertório (Listas e Músicas)

| ID | Requisito |
|---|---|
| RF-08 | O sistema deve permitir que o usuário crie uma lista, definindo um nome. |
| RF-09 | O sistema deve permitir adicionar uma música já existente no repertório a uma lista. |
| RF-10 | O sistema deve permitir remover uma música de uma lista, sem excluir a música do repertório geral. |
| RF-11 | O sistema deve permitir reordenar as músicas dentro de uma lista. |
| RF-12 | O sistema deve permitir renomear ou excluir uma lista. |

### Configuração por música

| ID | Requisito |
|---|---|
| RF-13 | O sistema deve permitir definir fonte, tom e velocidade para uma música, vinculados à combinação lista+música específica. |
| RF-14 | O sistema deve persistir localmente as alterações de fonte/tom/velocidade feitas dentro de uma lista. |
| RF-15 | O sistema deve manter e atualizar, para cada música, a última configuração usada (fonte/tom/velocidade), independente da lista em que foi tocada. |

### Motor de transposição

| ID | Requisito |
|---|---|
| RF-16 | O sistema deve calcular a transposição dos acordes de uma cifra dado um deslocamento em semitons, preservando a qualidade do acorde (maior, menor, sétima, sustenido/bemol, etc.). |
| RF-17 | O sistema deve exibir o tom transposto sem alterar a letra ou a estrutura da cifra. |

### Teleprompter

| ID | Requisito |
|---|---|
| RF-18 | O sistema deve exibir a cifra com rolagem automática, com velocidade ajustável em tempo real durante a leitura. |
| RF-19 | O sistema deve permitir pausar e retomar a rolagem automática. |
| RF-20 | O sistema deve permitir ajustar o tamanho da fonte em tempo real durante a leitura. |
| RF-27 | O sistema deve permitir reiniciar a música atual do início, mantendo fonte, tom e velocidade selecionados. |
| RF-28 | O sistema deve permitir avançar para a próxima música da lista, quando a música atual foi aberta no contexto de uma lista. |
| RF-29 | O sistema deve permitir retornar para a música anterior da lista, quando a música atual foi aberta no contexto de uma lista. |
| RF-30 | O sistema deve ocultar ou desabilitar os controles de próxima/anterior quando a música foi aberta fora do contexto de uma lista (ex: via busca global). |

### Busca

| ID | Requisito |
|---|---|
| RF-21 | O sistema deve permitir buscar uma música por título ou artista dentro de uma lista específica. |
| RF-22 | O sistema deve permitir buscar uma música por título ou artista em todo o repertório (todas as listas). |
| RF-23 | O sistema deve permitir abrir uma música diretamente a partir dos resultados de busca, sem exigir que ela pertença a uma lista. |

### Offline e persistência

| ID | Requisito |
|---|---|
| RF-24 | O sistema deve armazenar localmente toda música já importada (cifra + metadados), permitindo leitura sem conexão à internet. |
| RF-25 | O sistema deve armazenar localmente listas, ordenação e configurações por música, disponíveis sem conexão. |
| RF-26 | O sistema deve indicar ao usuário quando uma ação depende de conexão (ex: importar novo link) versus quando funciona normalmente offline (leitura, teleprompter). |

## 6. Requisitos Não-Funcionais

### Confiabilidade offline

| ID | Requisito |
|---|---|
| RNF-01 | Após uma música ter sido importada e salva localmente, 100% das funcionalidades de leitura (teleprompter, transposição, navegação entre músicas da lista) devem operar sem nenhuma chamada de rede. |
| RNF-02 | A ausência de conexão à internet não deve causar crash, tela de erro bloqueante, ou perda de dados já salvos localmente. |
| RNF-03 | O sistema deve indicar visualmente o status de conexão apenas em ações que dependem de rede (ex: importar novo link), nunca durante leitura/teleprompter. |

### Performance e responsividade

| ID | Requisito |
|---|---|
| RNF-04 | A troca entre músicas de uma lista (RF-28/RF-29) deve ocorrer em até 300ms, por se tratar de dado já persistido localmente — este é o requisito que resolve diretamente o problema da "canja" descrito na Seção 1. |
| RNF-05 | A busca (RF-21/RF-22) deve retornar resultados em até 300ms após o usuário parar de digitar (debounce). |
| RNF-06 | A rolagem automática do teleprompter deve se manter visualmente fluida, sem engasgos perceptíveis, independentemente do tamanho da cifra. |
| RNF-07 | O tempo de importação de uma cifra via link (parsing) deve ser comunicado ao usuário com indicador de progresso caso ultrapasse 2 segundos. |
| RNF-19 | O backend deve manter cache de cifras já parseadas, indexado pela URL de origem, evitando requisições repetidas ao Cifra Club para a mesma música. |

### Usabilidade e acessibilidade (uso em palco)

| ID | Requisito |
|---|---|
| RNF-08 | O tamanho mínimo de fonte configurável deve permanecer legível a uma distância de braço estendido. |
| RNF-09 | A interface do teleprompter deve manter contraste adequado para uso em ambientes com pouca luz (ex: tema escuro como padrão nessa tela). |
| RNF-10 | Os controles usados durante a performance devem ser acionáveis com o mínimo de toques possível, sem necessidade de navegação por menus. |

### Segurança

| ID | Requisito |
|---|---|
| RNF-11 | Senhas devem ser armazenadas exclusivamente como hash (bcrypt ou Argon2), nunca em texto puro, em nenhuma camada do sistema. |
| RNF-12 | Toda comunicação entre app e backend deve ocorrer exclusivamente via HTTPS. |
| RNF-13 | Tokens (access e refresh) devem ser armazenados localmente em local seguro (Android Keystore / EncryptedSharedPreferences), nunca em texto puro. |
| RNF-14 | O access token deve ter tempo de expiração curto (ex: 15-30 min); o refresh token deve permitir renovação sem exigir novo login. |

### Compatibilidade

| ID | Requisito |
|---|---|
| RNF-15 | O app deve suportar Android a partir da versão mínima definida na fase de design técnico (ex: Android 8.0/API 26 como piso razoável de mercado). |
| RNF-16 | A interface deve se adaptar a diferentes tamanhos de tela (celular e tablet). |

### Manutenibilidade e observabilidade

| ID | Requisito |
|---|---|
| RNF-17 | O código deve seguir Clean Architecture com separação de camadas (Presentation/Domain/Dados), especialmente para testes automatizados isolados do motor de transposição. |
| RNF-18 | Falhas de parsing do Cifra Club devem ser registradas via Crashlytics/Analytics, permitindo identificar aumento na taxa de falha (ex: mudança no HTML do site de origem). |

## 7. Critérios de Sucesso / Aceite

### Critérios de aceite

| Grupo | Critério de aceite |
|---|---|
| Autenticação | Cadastro, login, renovação automática de token e logout funcionam sem exceções não tratadas. Senha nunca aparece em texto puro em log, banco ou payload. |
| Importação de cifra | Backend extrai cifra corretamente em ≥95% de uma amostra de teste variada. Falha aciona fallback manual sem travar o fluxo. |
| Repertório | Criar, editar, reordenar e excluir listas/músicas reflete corretamente após reabrir o app. |
| Configuração por música | Alterar config numa lista não afeta a mesma música em outra lista. Fora de lista, mostra última configuração usada. |
| Motor de transposição | 100% de aprovação em suíte de testes unitários com casos comuns e de borda. |
| Teleprompter | Rolagem, pausa, ajustes em tempo real, reinício e navegação funcionam sem travamento. Próxima/anterior ocultos fora de contexto de lista. |
| Busca | Retorna resultado correto dentro de uma lista e em todo o repertório, dentro do tempo definido em RNF-05. |
| Offline | Em modo avião, 100% das músicas já importadas abrem e funcionam normalmente no teleprompter. |

### Critérios de sucesso do produto

1. **Teste de campo real:** app usado em situação real de performance sem recorrer a papel/PDF/site como plano B.
2. **Tempo de preparo:** importar e deixar uma música pronta pra leitura é mais rápido que buscar manualmente num site de cifras.
3. **Zero falha crítica offline:** nenhuma trava, perda de dados ou bloqueio de leitura de música já importada, sem internet.
4. **Suíte de testes automatizados passando**, especialmente no motor de transposição e nos use cases críticos.

## 8. Riscos e Dependências

### Riscos técnicos

| ID | Risco | Impacto | Mitigação |
|---|---|---|---|
| R-01 | Cifra Club muda a estrutura HTML da página, quebrando o parser (RF-05) | Alto | Fallback manual (RF-06); monitorar taxa de falha via Crashlytics (RNF-18); testes que fixam a estrutura esperada, quebrando de forma visível quando o site mudar |
| R-02 | Cifra Club bloqueia requisições automatizadas (rate limiting, bloqueio de IP/user-agent) | Médio | Fetch on-demand por ação do usuário (não scraping em massa); cache no backend por URL (RNF-19); retry com backoff exponencial |
| R-03 | Casos de acordes incomuns não previstos no motor de transposição | Médio | Suíte de testes ampla com casos de borda; logging de acordes não reconhecidos |
| R-04 | Performance da rolagem automática degrada em dispositivos mais fracos ou cifras muito longas | Médio | Testar em dispositivo de referência de entrada; perfil de performance antes de considerar o MVP concluído |
| R-05 | Vazamento de segredo de assinatura JWT, ou hash de senha malfeito | Alto | Seguir práticas padrão (Argon2/bcrypt, segredo via variável de ambiente), nunca reinventar criptografia própria |

### Riscos de escopo/produto

| ID | Risco | Impacto | Mitigação |
|---|---|---|---|
| R-06 | Scope creep — features de V2/V3 entrando no MVP | Médio | Escopo (Seção 4) registrado explicitamente; nova ideia é testada contra o Objetivo do MVP |
| R-07 | Projeto solo sem prazo — risco de se estender indefinidamente | Médio | Critérios de aceite (Seção 7) como linha de chegada objetiva |
| R-08 | Uso de conteúdo de terceiros (Cifra Club) levanta questão de direitos autorais se o projeto crescer além de uso pessoal | Baixo por agora | Manter enquadramento de ferramenta pessoal; revisitar se considerar publicação pública/comercial |

### Dependências externas

| ID | Dependência | Por quê |
|---|---|---|
| D-01 | Disponibilidade e estrutura do site Cifra Club | Fonte de dados da importação (RF-05) |
| D-02 | Firebase (Crashlytics/Analytics) | Observabilidade de falhas de parsing e crashes (RNF-18) |
| D-03 | Infraestrutura de hospedagem do backend próprio | Autenticação e endpoints consumidos pelo app |
| D-04 | Bibliotecas de terceiros: Retrofit, Room, Hilt (app); parser HTML, hash de senha, JWT (backend) | Base técnica dos requisitos funcionais |

## Anexo A — Decisões-chave (candidatas a ADR)

1. Autenticação própria no backend (JWT + refresh token), não Firebase Auth — motivo: treinar implementação de autenticação segura como skill de backend.
2. Fetch + parsing da cifra do Cifra Club acontece no backend, não no app — motivo: manutenção centralizada do parser, cache compartilhado, retry centralizado.
3. Navegação por próxima/anterior no teleprompter só existe em contexto de lista; fica oculta quando a música é aberta via busca global — motivo: evitar ambiguidade de "sequência" sem contexto definido.
4. Música aberta fora de uma lista usa a última configuração (fonte/tom/velocidade) usada, não um padrão neutro — motivo: preserva a intenção mais recente do usuário, menor surpresa.
