# Auth — Clinix

> Estratégia de autenticação, autorização, controle de acesso e segurança do Clinix.

---

# 1. VISÃO GERAL

O sistema de autenticação do Clinix deve ser:

* seguro
* escalável
* auditável
* multi-tenant
* moderno
* preparado para APIs
* preparado para mobile

---

# 2. OBJETIVOS

Garantir:

* autenticação segura
* isolamento entre clínicas
* autorização granular
* rastreabilidade
* proteção de dados
* experiência fluida
* suporte web e mobile

---

# 3. PRINCÍPIOS

Toda arquitetura de autenticação deve priorizar:

* segurança
* simplicidade operacional
* escalabilidade
* rastreabilidade
* experiência do usuário
* baixo acoplamento

---

# 4. ESTRATÉGIA OFICIAL

## Autenticação

# JWT Authentication

---

## Backend

* Django REST Framework
* Simple JWT

---

# 5. JWT

O JWT será responsável por:

* autenticação
* contexto do usuário
* contexto do tenant
* permissões básicas
* sessão stateless

---

# 6. PAYLOAD JWT

Exemplo:

```json
{
  "user_id": 10,
  "tenant_id": 3,
  "roles": [
    "admin"
  ]
}
```

---

# 7. TOKENS

O sistema utilizará:

* access token
* refresh token

---

# 8. ACCESS TOKEN

## Objetivo

Autenticação das requests.

---

## Características

* curta duração
* maior segurança
* renovável

---

# 9. REFRESH TOKEN

## Objetivo

Renovar sessões sem novo login.

---

# Características

* duração maior
* invalidável
* rastreável

---

# 10. LOGIN

O login deverá suportar:

* e-mail + senha
* login mobile
* login web
* login multi-dispositivo

---

# Futuramente

* SSO
* Google Login
* Microsoft Login
* biometria mobile

---

# 11. SENHAS

Senhas devem:

* ser criptografadas
* nunca ser armazenadas em texto puro
* seguir padrões fortes

---

# Requisitos mínimos

* tamanho mínimo
* proteção contra senhas fracas
* hash seguro

---

# 12. HASHING

Utilizar:

# Argon2

Ou fallback seguro do Django.

---

# 13. RESET DE SENHA

Fluxo obrigatório:

* token temporário
* expiração
* invalidação automática
* rastreamento

---

# Nunca permitir

* links eternos
* tokens reutilizáveis
* recuperação insegura

---

# 14. MFA

O sistema deve nascer preparado para:

# MFA (Multi-Factor Authentication)

---

# Futuramente

* TOTP
* aplicativo autenticador
* SMS
* WhatsApp
* e-mail OTP

---

# 15. AUTORIZAÇÃO

O sistema utilizará:

# RBAC

---

# 16. RBAC

Role-Based Access Control.

---

# Permissões devem considerar

* tenant
* papel
* módulo
* ação
* unidade
* contexto operacional

---

# 17. ROLES INICIAIS

Exemplos:

* admin
* gestor
* recepcionista
* médico
* dentista
* fisioterapeuta
* biomédico
* financeiro
* laboratório

---

# 18. PERMISSÕES

Permissões devem ser granulares.

---

# Exemplos

```txt
patients.view
patients.create
patients.update
patients.delete

financial.view
financial.manage

appointments.manage
```

---

# 19. CONTEXTO MULTI-TENANT

Toda autenticação deve considerar:

# tenant obrigatório

---

# Nunca permitir

* acesso cruzado
* sessão compartilhada incorreta
* troca indevida de tenant

---

# 20. LOGIN E TENANT

Todo login deve resolver:

* usuário
* tenant
* permissões
* contexto operacional

---

# 21. PROTEÇÃO DE ROTAS

Todas as rotas devem validar:

* autenticação
* tenant
* permissões
* contexto da operação

---

# 22. FRONTEND AUTH

Frontend deve possuir:

* proteção de rotas
* renovação automática de token
* logout automático
* controle de sessão
* tratamento de expiração

---

# 23. SESSÕES

Sessões devem ser:

* seguras
* rastreáveis
* invalidáveis
* isoladas

---

# 24. LOGOUT

Logout deve:

* invalidar refresh token
* encerrar sessão
* remover credenciais locais

---

# 25. DISPOSITIVOS

Futuramente o sistema poderá:

* listar dispositivos logados
* invalidar sessões específicas
* detectar acessos suspeitos

---

# 26. AUDITORIA

Toda ação crítica deve gerar logs.

---

# Eventos importantes

* login
* logout
* falha de login
* reset de senha
* alteração de senha
* alteração de permissões

---

# 27. LOGS DE SEGURANÇA

Registrar:

* usuário
* tenant
* IP
* user-agent
* horário
* ação

---

# 28. RATE LIMITING

Proteção obrigatória contra:

* brute force
* spam
* tentativas automatizadas

---

# Estratégias

* limite de tentativas
* cooldown
* bloqueio temporário

---

# 29. LOGIN SUSPEITO

Futuramente detectar:

* IPs incomuns
* localização anormal
* múltiplas falhas
* acessos suspeitos

---

# 30. LGPD

Toda autenticação deve respeitar:

* privacidade
* proteção de dados
* rastreabilidade
* consentimento quando necessário

---

# 31. MOBILE AUTH

A autenticação deve ser preparada para:

* Android
* iOS
* refresh automático
* biometria futura

---

# 32. API SECURITY

Toda API deve considerar:

* JWT válido
* tenant válido
* permissões
* auditoria
* rate limiting

---

# Nunca permitir

* endpoints públicos indevidos
* bypass de autenticação
* permissões inseguras

---

# 33. TOKENS SENSÍVEIS

Nunca armazenar:

* tokens em texto inseguro
* segredos no frontend
* credenciais hardcoded

---

# 34. ENV VARIABLES

Credenciais devem ficar em:

```txt
.env
```

---

# Nunca permitir

* segredos commitados
* chaves no repositório
* tokens expostos

---

# 35. COOKIES VS STORAGE

Inicialmente:

# JWT em storage seguro

---

# Futuramente avaliar

* cookies httpOnly
* estratégias híbridas
* proteção avançada

---

# 36. WEBSOCKETS

Realtime deve validar:

* autenticação
* tenant
* permissões

---

# Nunca permitir

* conexão realtime sem autenticação

---

# 37. WEBHOOKS

Integrações externas devem possuir:

* assinatura
* autenticação
* validação
* rastreabilidade

---

# 38. ADMINISTRADORES

Administradores devem possuir:

* permissões elevadas
* logs detalhados
* rastreamento completo

---

# 39. SEGREGAÇÃO DE ACESSO

O sistema deve permitir:

* restrição por unidade
* restrição por módulo
* restrição por função

---

# 40. FUTURO ENTERPRISE

Preparar arquitetura para:

* SSO
* OAuth2
* OpenID Connect
* Active Directory
* Azure AD

---

# 41. TESTES

O sistema deve possuir testes para:

* login
* permissões
* JWT
* RBAC
* tenancy
* expiração
* segurança

---

# 42. O QUE EVITAR

Nunca permitir:

* permissões globais inseguras
* sessões inválidas
* queries sem tenant
* bypass de RBAC
* endpoints desprotegidos
* tokens inseguros

---

# 43. FILOSOFIA DE SEGURANÇA

A autenticação do Clinix deve transmitir:

* confiança
* robustez
* rastreabilidade
* previsibilidade
* segurança enterprise

---

# 44. PERMISSÕES POR ESPECIALIDADE

O RBAC do Clinix considera **especialidade** como dimensão de permissão além de role + módulo.

## Roles típicas

* `admin` — gestão da clínica
* `gestor` — operação + relatórios
* `recepcionista` — agenda + check-in + cobrança avulsa
* `profissional` — atendimento clínico (com escopo por especialidade)
* `financeiro` — financeiro + relatórios financeiros
* `tecnico_lab` (Fase 2.5) — operação laboratorial

## Permissões por especialidade

Cada profissional tem 1+ especialidades atribuídas. Aciona:

* somente templates de prontuário daquela especialidade são oferecidos
* componentes específicos (odontograma, body map, antropometria) só aparecem para profissionais habilitados
* aplicação de escalas de psicologia restrita a `specialty=psychology`
* prescrição tópica de dermato restrita a profissionais com habilitação dermato
* validação de procedimentos estéticos só por habilitados

## Sigilo psicológico (camada extra)

`RecordEntry.is_private=True` (padrão em psicologia):

* leitura restrita ao profissional autor
* outros profissionais veem que existe entrada, sem conteúdo
* `break_glass` exige justificativa + alerta + audit log
* admin do tenant nunca vê em condição normal
* admin Clinix nunca vê em prod

## Object permissions

`django-guardian` aplica permissões por objeto:

* paciente compartilhado entre vários profissionais com permissão de visualização
* prontuário restrito ao autor + supervisor (se houver)
* foto clínica restrita ao profissional que capturou e ao paciente

## Validação no backend e frontend

* backend valida em todo endpoint (`HasModulePermission`, `HasSpecialty`, `IsObjectOwner`)
* frontend esconde ações sem permissão (`<Can permission="...">`)
* tentativa de bypass gera audit log + alerta

## Multi-especialidade

Profissional pode ter mais de uma especialidade (ex: médico que atua em dermato e estética). As permissões somam-se sem se cancelar.

Detalhes em `docs/templates-clinicos.md`, `docs/escalas-testes.md`, `docs/dermatologia.md`.

---

# 45. OBJETIVO FINAL

Construir uma camada de autenticação:

* segura
* moderna
* escalável
* preparada para mobile
* preparada para APIs
* preparada para operação enterprise

Sem perder:

* simplicidade
* fluidez
* produtividade
* experiência premium
