# Sprint 01 — Fundação

> Primeira sprint do Clinix. Foco em arquitetura sólida antes de qualquer feature de usuário.

---

# 1. OBJETIVO

Subir a base técnica do Clinix:

* ambiente de desenvolvimento padronizado
* projeto Django funcionando
* projeto Next.js funcionando
* multi-tenancy operacional
* autenticação básica
* CI/CD funcional

Sem essa base, nada do MVP pode ser construído com qualidade.

---

# 2. DURAÇÃO ESTIMADA

2–3 semanas.

---

# 3. ESCOPO

## Backend

* [ ] estrutura de repositório (`backend/`, `frontend/`, `infrastructure/`, `docs/`, etc.)
* [ ] Dockerfile do backend
* [ ] docker-compose com Postgres, Redis, backend, frontend
* [ ] Django 5 + DRF instalados
* [ ] settings divididos (base, dev, staging, production)
* [ ] env vars via `.env` + `.env.example`
* [ ] app `core` criado com:
  * [ ] modelo `Tenant`
  * [ ] modelo `User` customizado
  * [ ] modelo `Role` + `Permission`
  * [ ] `TenantAwareModel` (abstract)
  * [ ] `TenantAwareManager`
  * [ ] middleware de resolução de tenant
  * [ ] middleware de auditoria de queries (dev only)
* [ ] migrations iniciais
* [ ] superuser de seed para desenvolvimento
* [ ] autenticação JWT com SimpleJWT
* [ ] login endpoint
* [ ] refresh endpoint
* [ ] me endpoint
* [ ] testes de isolamento marcados `pytest -m tenancy`

---

## Frontend

* [ ] Next.js 15 com App Router
* [ ] TypeScript estrito
* [ ] Tailwind CSS 4 instalado
* [ ] shadcn/ui inicializado
* [ ] design system inicial: cores, tipografia, spacing
* [ ] estrutura de pastas: `app/`, `components/`, `modules/`, `services/`, `hooks/`, `stores/`, `lib/`, `types/`
* [ ] cliente HTTP configurado (axios ou fetch wrapper)
* [ ] interceptor de auth (refresh automático)
* [ ] TanStack Query configurado
* [ ] Zustand para auth store
* [ ] tela de login funcional
* [ ] proteção de rotas básica
* [ ] layout autenticado base (sidebar + header)

---

## Infraestrutura

* [ ] Dockerfile do frontend
* [ ] Nginx no docker-compose
* [ ] reverse proxy para backend e frontend
* [ ] script de bootstrap (`scripts/setup.sh`)
* [ ] script de seed (`scripts/seed.sh`)
* [ ] script de backup local (`scripts/backup.sh`)

---

## CI/CD

* [ ] workflow do GitHub Actions
* [ ] lint backend (Ruff + Black)
* [ ] lint frontend (ESLint + Prettier)
* [ ] testes backend (pytest)
* [ ] testes frontend (Vitest)
* [ ] build de imagens Docker
* [ ] healthcheck endpoint validado

---

## Observabilidade

* [ ] Sentry configurado em backend e frontend (DSN via env)
* [ ] logs estruturados em JSON
* [ ] request_id propagado
* [ ] healthcheck `/api/healthz`
* [ ] readiness `/api/readyz` validando DB + Redis

---

# 4. ENTREGÁVEIS

Ao fim da sprint:

* `docker compose up` sobe tudo localmente
* desenvolvedor consegue logar com superuser
* tela inicial autenticada renderiza
* CI roda lint + testes em todo PR
* primeiro tenant de teste já isolado
* base preparada para começar Sprint 02

---

# 5. NÃO ESTÁ NO ESCOPO

* MFA (Sprint 02)
* RBAC granular completo (Sprint 02)
* CRUD de pacientes (Sprint 03)
* qualquer feature de UI além do login
* deploy em VPS (Sprint 02 ou 03)

---

# 6. RISCOS

* configuração inicial de tenancy errada pode contaminar todo o futuro
  * mitigação: testes de isolamento desde o dia 1
* divergência entre ambientes dev/staging/prod
  * mitigação: docker-compose único, settings parametrizado
* Sentry capturando dados sensíveis
  * mitigação: scrubbing de PII configurado desde o início

---

# 7. CRITÉRIOS DE ACEITAÇÃO POR ITEM

Cada checkbox só vira ✅ quando:

* código no `main`
* testes passando
* code review aprovado
* documentado em comentário ou doc se for decisão não óbvia

---

# 8. PRÓXIMA SPRINT

Sprint 02 cobrirá:

* MFA TOTP
* RBAC granular completo
* CRUD de pacientes
* primeiros endpoints de agenda

Ver `tasks/sprint-02.md`.
