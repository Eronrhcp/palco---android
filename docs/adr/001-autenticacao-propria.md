# ADR-001: Autenticação própria no backend (JWT + refresh token)

## Status
Aceito

## Contexto
O Palco precisa de autenticação para separar dados por usuário desde o
MVP. Duas opções foram consideradas: usar um serviço gerenciado (Firebase
Auth), que cuida de senha, tokens e login social praticamente sem código
próprio; ou implementar autenticação inteiramente no backend próprio
(cadastro, hash de senha, emissão de JWT e refresh token).

Um objetivo explícito do projeto é treinar habilidades de backend a
sério, a proposito login social não é uma
necessidade imediata do MVP.

## Decisão
Autenticação será implementada no backend próprio: cadastro com e-mail e
senha, hash de senha com bcrypt ou Argon2, emissão de access token (JWT)
de curta duração e refresh token para renovação sem novo login.

## Consequências

**Positivas:**
- Login seguro com hash de senha e gestão de token.
- Controle total sobre o fluxo de autenticação, sem dependência de
  serviço de terceiro para essa funcionalidade central.

**Negativas:**
- Mais código e responsabilidade de segurança por conta própria (risco
  de implementação incorreta, mitigado por seguir práticas padrão —
  nunca reinventar criptografia).
- Sem login social no MVP — funcionalidade útil de UX fica para V2.
- Fluxos que Firebase Auth daria de graça (ex: recuperação de senha via
  e-mail) precisam ser implementados manualmente quando chegar a vez.
