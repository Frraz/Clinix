# Fase 0 — Fundação Técnica

> Base arquitetural que sustenta tudo o que virá depois. Sem esta fase, módulos não podem ser construídos com qualidade.

---

# 1. OBJETIVO

Subir o esqueleto completo do Clinix:

* monorepo organizado conforme `docs/project-map.md`
* Django + DRF + Postgres + Redis + Celery funcionando
* multi-tenancy operacional (row-based)
* autenticação JWT + MFA TOTP
* RBAC granular
* design system inicial implementado
* CI/CD funcional
* observabilidade configurada
* primeiro deploy em staging

Ao final, **qualquer feature nova nasce já multi-tenant, auditada e deployável**.

---

# 2. PRÉ-REQUISITOS

Toda a Pré-Fase 0 concluída (ver `docs/roadmap/00-preparacao.md`):

* design system aprovado
* contas externas provisionadas
* ambiente local funcional
* CI configurado (sem código ainda)
* clínica piloto identificada
* templates clínicos validados

---

# 3. DURAÇÃO ESTIMADA

**5–7 semanas** (Sprint 01: 2–3 sem + Sprint 02: 3–4 sem).

Reduzir abaixo disso compromete a base. Estender além disso indica scope creep.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Dev fullstack | 1 (founder) | 1 backend + 1 frontend |
| Designer | ad-hoc | 1 dedicado |
| QA | — | parcial |
| DevOps | — | parcial |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 01 — Fundação Técnica (2–3 semanas)

Arquivo dedicado: `tasks/sprint-01.md`.

### Foco

* monorepo + Docker Compose
* Django + DRF rodando
* multi-tenancy operacional
* autenticação JWT base
* Next.js + Tailwind + shadcn/ui inicializado
* CI/CD pipeline operacional
* Sentry + logs estruturados configurados

### Entregáveis principais

* [ ] `docker compose up` sobe tudo localmente
* [ ] `pytest -m tenancy` passa
* [ ] superuser de seed login com sucesso
* [ ] CI verde em PR
* [ ] healthcheck `/api/healthz` e `/api/readyz`
* [ ] deploy automatizado configurado (não usado ainda em prod)

### Decisões arquiteturais aplicadas

* `ADRs/001-api-first.md` — REST API versionada
* `ADRs/002-multi-tenant.md` — row-based + TenantAwareManager
* `ADRs/003-postgresql.md` — Postgres 16 + JSONB + RLS
* `ADRs/004-nextjs.md` — Next.js 15 + App Router
* `ADRs/005-rbac.md` — RBAC granular + django-guardian
* `ADRs/006-event-driven.md` — Django signals + Celery
* `ADRs/010-observabilidade-sentry.md` — Sentry + structlog

---

## 5.2 Sprint 02 — Auth Completo + Pacientes + Agenda Base (3–4 semanas)

Arquivo dedicado: `tasks/sprint-02.md`.

### Foco

* MFA TOTP (django-otp)
* RBAC seed (admin, gestor, recepcionista, profissional, financeiro)
* CRUD completo de pacientes
* Agenda base com FullCalendar
* R2 configurado para uploads
* Deploy em staging operacional

### Entregáveis principais

* [ ] MFA ativável + obrigatório para admin/profissional
* [ ] backup codes funcionais
* [ ] 50+ pacientes de teste cadastrados via CRUD
* [ ] 20+ consultas agendadas com FullCalendar drag-and-drop
* [ ] check-in funcionando
* [ ] upload de foto do paciente via R2 (presigned PUT)
* [ ] staging em `staging.clinix.app` acessível por HTTPS
* [ ] backup diário Postgres → R2 rodando

### Decisões arquiteturais aplicadas

* `ADRs/007-storage-cloudflare-r2.md` — R2 + presigned PUT
* `ADRs/010-observabilidade-sentry.md` — backup com tracking

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Responsável | Dependência | Risco | Prioridade |
|-----------|-------------|-------------|-------|------------|
| Docker Compose completo | dev | — | baixo | P0 |
| Multi-tenancy middleware + manager | backend | Postgres | crítico | P0 |
| JWT auth (login/refresh/me) | backend | django | baixo | P0 |
| Design system tokens no Tailwind | frontend | designer | médio | P0 |
| shadcn/ui base | frontend | Tailwind | baixo | P0 |
| MFA TOTP | backend | django-otp | médio | P0 |
| RBAC seed + decorator | backend | django-guardian | médio | P0 |
| CRUD pacientes (BE + FE) | fullstack | RBAC | médio | P0 |
| Agenda FullCalendar | frontend | API agenda | alto | P0 |
| Upload R2 (presigned) | fullstack | R2 conta | médio | P0 |
| CI lint + tests | devops | — | baixo | P0 |
| Sentry + logs JSON | devops | DSN | baixo | P0 |
| Staging deploy automatizado | devops | VPS | alto | P0 |
| Backup automatizado | devops | R2 | médio | P0 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que | Doc |
|------|----------|-----|
| Django 5 | framework backend | `docs/stack.md` |
| Django REST Framework | API | `docs/api.md` |
| django-otp | MFA | `docs/auth.md` |
| django-guardian | RBAC por objeto | `docs/auth.md` |
| django-auditlog | auditoria | `docs/auditoria.md` |
| SimpleJWT | tokens JWT | `docs/auth.md` |
| Celery + Redis | filas | `docs/stack.md` |
| Postgres 16 | banco | `ADRs/003-postgresql.md` |
| Next.js 15 + React 19 | frontend | `ADRs/004-nextjs.md` |
| Tailwind CSS 4 + shadcn/ui | UI | `docs/design-system.md` |
| TanStack Query | data fetching | `docs/stack.md` |
| Zustand | estado global | `docs/stack.md` |
| Sentry | erros | `ADRs/010-observabilidade-sentry.md` |
| structlog | logs JSON | `ADRs/010-observabilidade-sentry.md` |
| GitHub Actions | CI/CD | `docs/stack.md` |
| Cloudflare R2 + django-storages | storage | `ADRs/007-storage-cloudflare-r2.md` |
| pytest-django + factory_boy | testes | `docs/stack.md` |
| Vitest + Testing Library | testes FE | `docs/stack.md` |

---

# 8. KPIs DE SAÍDA

* [ ] CI verde em todas as branches protegidas
* [ ] testes de tenancy passam com 100% (zero vazamento entre tenants)
* [ ] cobertura de testes ≥ 60% no core
* [ ] staging deploy < 5 minutos
* [ ] `make setup` < 30 minutos em máquina nova
* [ ] zero erros críticos no Sentry após 1 semana de staging
* [ ] 5+ usuários de teste com roles diferentes
* [ ] 50+ pacientes cadastrados
* [ ] 20+ agendamentos criados

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Tenancy mal implementada vaza dados | baixa | crítico | testes obrigatórios desde dia 1 + RLS no Postgres |
| MFA bloqueia admin sem recuperação | média | crítico | backup codes + reset via email + comando admin |
| Upload R2 sem validação vira vetor | média | alto | validar mime/tamanho/extensão server-side |
| FullCalendar pesado em mobile | média | médio | testar responsividade desde início |
| CI lento | alta | baixo | paralelizar lint + tests + cache de deps |
| Sentry capturando PII | alta | crítico | scrubbing configurado no dia 1 |
| Founder gargalo (1 dev) | alta | alto | timeboxing rigoroso + features cortáveis |

---

# 10. ANTI-METAS

NÃO fazer nesta fase:

* nenhum módulo de domínio além de pacientes e agenda
* não implementar prontuário (Sprint 03)
* não implementar financeiro (Sprint 03)
* não conectar WhatsApp (Sprint 04)
* não implementar portal do paciente (Sprint 04)
* não otimizar prematuramente
* não construir customizações por clínica
* não tentar produção real (apenas staging)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| VPS Hostinger (staging) | R$ 90/mês × 2 meses = R$ 180 |
| Cloudflare R2 (storage baixo) | R$ 0 (free tier) |
| Domínio | R$ 50 (já feito Pré-Fase) |
| Sentry | R$ 0 (free tier) |
| GitHub Actions | R$ 0 (free tier) |
| Designer (refinamento) | R$ 1.000–2.000 |
| **Total adicional** | **~R$ 1.200–2.200** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

A Fase 0 só termina quando **todos os KPIs da seção 8** estão verdes E:

* [ ] qualquer dev novo consegue rodar localmente em < 30 min
* [ ] qualquer feature nova naturalmente herda multi-tenancy, audit e RBAC
* [ ] deploy em staging é um clique
* [ ] code review existe como prática
* [ ] documentação interna foi atualizada conforme decisões reais

Quando esses pontos estão verdes, **Sprint 03 pode começar imediatamente**.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market**: paralelamente, conversas com clínica piloto continuam (mockups Figma já dão para mostrar a UX)
* **Cadência operacional**: code review obrigatório começa aqui; primeiro retro ao final da Sprint 01

---

# 14. REFERÊNCIAS

* `tasks/sprint-01.md` — execução detalhada da Sprint 01
* `tasks/sprint-02.md` — execução detalhada da Sprint 02
* `docs/arquitetura.md`
* `docs/database-schema.md`
* `docs/tenancy.md`
* `docs/auth.md`
* `docs/api.md`
* `docs/stack.md`
* `ADRs/001-api-first.md` a `ADRs/010-observabilidade-sentry.md`
* `docs/roadmap/02-mvp.md` (próxima fase)
