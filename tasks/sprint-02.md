# Sprint 02 — Auth completo + Pacientes + Agenda base

> Segunda sprint do Clinix. Foco em segurança completa e primeiros módulos de domínio.

---

# 1. OBJETIVO

Completar a camada de autenticação/permissão e entregar os dois primeiros módulos de domínio:

* pacientes (CRUD completo)
* agenda (base, sem todas as features ainda)

Ao fim desta sprint, o sistema já deve permitir cadastrar pacientes e marcar consultas básicas.

---

# 2. DURAÇÃO ESTIMADA

3–4 semanas.

---

# 3. PRÉ-REQUISITOS

Sprint 01 concluída:

* base Django + Next.js rodando
* multi-tenancy operacional
* autenticação JWT base funcionando

---

# 4. ESCOPO

## Auth completo

* [ ] MFA TOTP via django-otp
* [ ] fluxo de ativação de MFA pelo usuário
* [ ] obrigatoriedade de MFA para perfis admin e médico
* [ ] backup codes
* [ ] reset de senha por email (token + expiração)
* [ ] alteração de senha pelo usuário logado
* [ ] rate limiting nos endpoints de auth (django-ratelimit)
* [ ] logout invalida refresh token
* [ ] testes de auth e MFA

---

## RBAC completo

* [ ] modelos `Role`, `Permission`, `RolePermission`, `UserRole`
* [ ] roles seed: admin, gestor, recepcionista, médico, financeiro
* [ ] permissões seed por módulo/ação
* [ ] decorator/permission_class `HasModulePermission`
* [ ] middleware injeta permissões no contexto
* [ ] hook frontend `usePermissions()`
* [ ] componente `<Can permission="...">` no frontend
* [ ] tela de gestão de usuários (admin)
* [ ] tela de gestão de roles
* [ ] tela de atribuição de roles a usuários

---

## Pacientes

* [ ] modelo `Patient` com todos os campos
* [ ] modelo `PatientAddress`, `PatientContact`, `PatientDocument`
* [ ] validação de CPF (mas não bloquear duplicidade, apenas alertar)
* [ ] CRUD endpoints REST
* [ ] busca via pg_trgm (fuzzy + acento-insensível)
* [ ] paginação + filtros
* [ ] soft delete
* [ ] consentimentos LGPD na criação
* [ ] upload de foto via R2
* [ ] tela de listagem
* [ ] tela de perfil do paciente
* [ ] tela de criação/edição
* [ ] tela de timeline (vazia por enquanto, estrutura pronta)

---

## Agenda base

* [ ] modelo `AppointmentType`
* [ ] modelo `Appointment`
* [ ] modelo `AppointmentStatusHistory`
* [ ] endpoint listar consultas com filtros
* [ ] endpoint criar consulta
* [ ] endpoint cancelar consulta (com motivo)
* [ ] endpoint reagendar
* [ ] endpoint mudar status (check-in, em atendimento, concluído, no-show)
* [ ] validação de conflito (lock otimista)
* [ ] tela de agenda diária (FullCalendar)
* [ ] tela de criação de consulta
* [ ] tela de detalhes da consulta
* [ ] integração com perfil do paciente (botão "agendar")

---

## Infraestrutura

* [ ] R2 configurado para uploads de foto
* [ ] django-storages integrado
* [ ] deploy em staging (Hostinger) funcionando
* [ ] HTTPS via Cloudflare
* [ ] backup diário do Postgres para R2

---

## Auditoria

* [ ] modelos do core registrados em django-auditlog
* [ ] visualização básica de audit log no admin

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* clínica de teste tem 5+ usuários com diferentes roles
* MFA habilitado para admin
* 50+ pacientes cadastrados
* 20+ consultas agendadas
* fluxo de check-in funcional
* staging acessível por HTTPS

---

# 6. NÃO ESTÁ NO ESCOPO

* prontuário (Sprint 03)
* financeiro (Sprint 03)
* WhatsApp (Sprint 04)
* automações (Sprint 04+)
* portal do paciente (Sprint 04+)
* recorrência avançada na agenda

---

# 7. RISCOS

* MFA mal implementado pode bloquear usuários reais
  * mitigação: backup codes + recovery via email obrigatórios
* validação de CPF muito restritiva quebra cadastro
  * mitigação: alertar duplicidade, não bloquear
* FullCalendar pesado em telas mobile
  * mitigação: responsividade testada desde o início
* upload de foto sem validação cria vetor de ataque
  * mitigação: validar mime-type + tamanho + extensão

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando
* code review aprovado
* validação manual em staging
* sem regressão na suíte de testes de tenancy

---

# 9. PRÓXIMA SPRINT

Sprint 03 cobrirá:

* prontuário base (SOAP, anexos, prescrições, atestados)
* financeiro essencial (cobrança, PIX, cartão)
* dashboards operacionais básicos
