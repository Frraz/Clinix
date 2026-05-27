# Clinix

> Plataforma operacional inteligente para clínicas, laboratórios e operações de saúde regionais.

**Status atual:** planejamento e arquitetura concluídos · pronto para iniciar Sprint 01

---

# Sumário

1. [Sobre o projeto](#sobre-o-projeto)
2. [Para quem é](#para-quem-é)
3. [Diferenciais](#diferenciais)
4. [Stack oficial](#stack-oficial)
5. [Arquitetura](#arquitetura)
6. [Estrutura do repositório](#estrutura-do-repositório)
7. [Documentação](#documentação)
8. [Roadmap](#roadmap)
9. [Status e próximos passos](#status-e-próximos-passos)
10. [Princípios](#princípios)
11. [Como começar](#como-começar)
12. [Licença e autoria](#licença-e-autoria)

---

# Sobre o projeto

O Clinix é uma plataforma SaaS para gestão operacional de clínicas e laboratórios, construída para entregar **UX premium, automações profundas, WhatsApp nativo e prontuário composable** que serve múltiplas especialidades com a mesma engine.

O foco inicial é o **mercado regional brasileiro** (Norte/Nordeste), onde a maioria das clínicas pequenas e médias ainda opera com sistemas legados, planilhas e WhatsApp manual. O Clinix oferece uma alternativa moderna, integrada e mobile-ready.

**Visão de longo prazo:** evoluir para uma *Health Operations Platform* — plataforma completa de operação, automação, gestão e inteligência em saúde.

---

# Para quem é

O Clinix atende **8 especialidades-alvo já no MVP**:

| Especialidade | Componentes específicos |
|---------------|-------------------------|
| Clínicas médicas gerais | Prontuário SOAP, prescrição, atestado |
| Odontologia | Odontograma FDI, plano de tratamento, parcelamento |
| Fisioterapia | Avaliação funcional, sessões, evolução, alta |
| Psicologia | Escalas validadas (PHQ-9, GAD-7, DASS-21), sigilo reforçado |
| Biomedicina / Laboratórios | Solicitação + entrega de laudo (operação completa na Fase 2.5) |
| Dermatologia | Mapa corporal de lesões, dermatoscopia, prescrição tópica |
| Clínicas de Estética | Pacotes de tratamento, fotos antes/depois, contraindicações |
| Nutrição | Antropometria, bioimpedância, plano alimentar, recordatório |

Preparado para evoluir para centros de diagnóstico, telemedicina, multi-especialidade e redes regionais.

---

# Diferenciais

- **UX moderna** — referência visual: Linear, Notion, Figma (não ERP de 2010)
- **Prontuário composable** — uma engine de templates serve as 8 especialidades
- **WhatsApp nativo** — Evolution API, sem dependência de Twilio
- **Automações operacionais** — confirmação, lembrete, pós-consulta, cobrança, recuperação de faltoso
- **Multi-tenancy** desde o dia 1 — isolamento real entre clínicas
- **Mobile-ready** — apps Paciente e Profissional na Fase 4
- **IA útil** (Fase 3) — sumarização clínica, sugestão de CID, previsão de no-show
- **LGPD first-class** — consentimento por foto, anonimização, portabilidade
- **Sem aprisionar dados** — export completo a qualquer momento (LGPD art. 18, V)
- **Suporte regional** — atendimento próximo, conhecendo o cliente

---

# Stack oficial

## Backend
| Camada | Tech |
|--------|------|
| Linguagem | Python 3.12 |
| Framework | Django 5 + Django REST Framework |
| Banco | PostgreSQL 16 (JSONB, RLS, pg_trgm) |
| Cache / fila | Redis 7 |
| Tasks assíncronas | Celery 5 + Celery Beat |
| Auth | SimpleJWT + django-otp (MFA TOTP) |
| RBAC | django-guardian + roles customizadas |
| Auditoria | django-auditlog |
| Realtime | Django Channels (Fase 1.5+) |

## Frontend Web
| Camada | Tech |
|--------|------|
| Framework | Next.js 15 (App Router) |
| Linguagem | TypeScript 5 (estrito) |
| Estilo | Tailwind CSS 4 |
| Componentes | shadcn/ui |
| Estado server | TanStack Query v5 |
| Estado global | Zustand |
| Formulários | React Hook Form + Zod |
| Editor prontuário | TipTap |
| Agenda | FullCalendar |
| Gráficos | Recharts |

## Mobile (Fase 4)
React Native · Expo SDK 52+ · Expo Router · MMKV · Expo Notifications (FCM)

## Integrações
WhatsApp (Evolution API self-hosted) · Mercado Pago (PIX/boleto/cartão) · Stripe (billing SaaS) · Resend (email) · Cloudflare R2 (storage) · WeasyPrint (PDFs) · OpenAI/Anthropic API (IA, Fase 3)

## Infra & DevOps
Docker · Docker Compose · Nginx · Gunicorn + Daphne · Hostinger VPS · Cloudflare (CDN/DNS/SSL/R2) · GitHub Actions · Sentry · structlog · pg_dump + WAL (Fase 2+)

## Testes
pytest-django · factory_boy · Vitest · Testing Library · Playwright · Ruff + Black + ESLint + Prettier

Detalhamento e justificativas em [docs/stack.md](docs/stack.md) e nas [ADRs/](ADRs/).

---

# Arquitetura

**Modular Monolith** evoluindo para arquitetura desacoplada conforme demanda:

```
                  ┌──────────────────────────┐
   Web (Next.js)  │                          │
        ◄────────►│                          │
                  │     Django REST API      │
   Mobile (RN)    │       (API-first)        │
        ◄────────►│                          │
                  │  Multi-tenant (row-based)│
   Integrações    │  RBAC + LGPD + Audit     │
        ◄────────►│                          │
                  └────┬─────────────┬───────┘
                       │             │
                  ┌────▼────┐   ┌────▼─────┐
                  │Postgres │   │  Redis   │
                  │  + RLS  │   │ + Celery │
                  └─────────┘   └──────────┘
                       │
                  ┌────▼──────┐
                  │Cloudflare │
                  │    R2     │
                  └───────────┘
```

Características:

- **API-first** — mesma API serve web, mobile, integrações e webhooks
- **Multi-tenant** row-based via `tenant_id` + middleware + manager
- **Event-driven** — Django signals + Celery + workflow engine
- **RBAC granular** — roles + permissões por módulo + por objeto (django-guardian) + por especialidade
- **Auditoria** — django-auditlog em todos os modelos críticos
- **Observabilidade** — Sentry + logs JSON estruturados + `request_id` propagado

Decisões formais em [ADRs/](ADRs/) (001 a 010).

---

# Estrutura do repositório

```txt
Clinix/
├── ADRs/                      → decisões arquiteturais (10 ADRs)
├── backend/                   → Django + DRF (a ser construído)
├── frontend/                  → Next.js + TypeScript (a ser construído)
├── infrastructure/            → Docker, Nginx, scripts (a ser construído)
├── docs/                      → documentação técnica e de produto (43 arquivos)
│   └── roadmap/               → roadmap detalhado por fase (12 arquivos)
├── tasks/                     → MVP, backlog, sprints, bugs (11 arquivos)
├── examples/                  → fluxos canônicos do sistema (10 arquivos)
├── CLAUDE.md                  → instruções globais para o Claude Code
├── ORDEM-DE-CRIACAO.TXT       → ordem cronológica de criação dos docs
├── POSSÍVEIS CLIENTES.MD      → leads regionais (Rondon do Pará)
└── README.md                  → este arquivo
```

Convenções estruturais em [docs/project-map.md](docs/project-map.md).

---

# Documentação

A documentação é o pilar do projeto: **86 arquivos markdown** cobrindo estratégia, arquitetura, módulos, fluxos, roadmap e operação.

## Por onde começar

| Você é... | Comece por |
|-----------|------------|
| Founder / executivo | [docs/vision.md](docs/vision.md) → [docs/roadmap.md](docs/roadmap.md) → [docs/roadmap/10-go-to-market.md](docs/roadmap/10-go-to-market.md) |
| Dev novo no projeto | [CLAUDE.md](CLAUDE.md) → [docs/arquitetura.md](docs/arquitetura.md) → [docs/stack.md](docs/stack.md) → [docs/roadmap/00-preparacao.md](docs/roadmap/00-preparacao.md) |
| Designer | [docs/design-system.md](docs/design-system.md) → [docs/funcionalidades.md](docs/funcionalidades.md) → [examples/](examples/) |
| Product manager | [docs/funcionalidades.md](docs/funcionalidades.md) → [tasks/mvp.md](tasks/mvp.md) → [docs/roadmap.md](docs/roadmap.md) |
| Investidor / stakeholder | [docs/vision.md](docs/vision.md) → [docs/roadmap.md](docs/roadmap.md) seção 4 |
| Customer Success | [docs/onboarding.md](docs/onboarding.md) → [docs/suporte.md](docs/suporte.md) → [examples/fluxo-onboarding.md](examples/fluxo-onboarding.md) |

## Estratégia e visão

- [docs/vision.md](docs/vision.md) — visão estratégica, posicionamento, propósito
- [docs/funcionalidades.md](docs/funcionalidades.md) — mapa completo de funcionalidades
- [docs/regras-de-negocio.md](docs/regras-de-negocio.md) — regras de negócio do sistema
- [docs/roadmap.md](docs/roadmap.md) — master index do roadmap

## Arquitetura e técnica

- [docs/arquitetura.md](docs/arquitetura.md) — arquitetura geral
- [docs/stack.md](docs/stack.md) — stack e justificativas
- [docs/api.md](docs/api.md) — desenho da API
- [docs/auth.md](docs/auth.md) — autenticação, MFA, RBAC, sigilo
- [docs/tenancy.md](docs/tenancy.md) — multi-tenancy
- [docs/database-schema.md](docs/database-schema.md) — modelagem do banco
- [docs/design-system.md](docs/design-system.md) — design system
- [docs/performance.md](docs/performance.md) — SLOs e otimizações
- [docs/backup-dr.md](docs/backup-dr.md) — backup e disaster recovery

## Módulos de domínio

- [docs/prontuario.md](docs/prontuario.md) — prontuário composable
- [docs/laboratorio.md](docs/laboratorio.md) — módulo laboratorial
- [docs/whatsapp.md](docs/whatsapp.md) — integração WhatsApp
- [docs/convenios.md](docs/convenios.md) — convênios e TISS
- [docs/estoque.md](docs/estoque.md) — controle de estoque
- [docs/crm.md](docs/crm.md) — CRM da clínica
- [docs/portal-paciente.md](docs/portal-paciente.md) — portal do paciente
- [docs/workflows.md](docs/workflows.md) — eventos e workflows
- [docs/notificacoes.md](docs/notificacoes.md) — notificações
- [docs/mobile.md](docs/mobile.md) — estratégia mobile
- [docs/ia.md](docs/ia.md) — inteligência artificial

## Especialidades específicas

- [docs/dermatologia.md](docs/dermatologia.md) — dermatologia (mapa de lesões, dermatoscopia)
- [docs/estetica.md](docs/estetica.md) — clínicas de estética (pacotes, antes/depois)
- [docs/nutricao.md](docs/nutricao.md) — nutrição (plano alimentar, antropometria)

## Componentes transversais

- [docs/templates-clinicos.md](docs/templates-clinicos.md) — engine de templates do prontuário
- [docs/imagens-clinicas.md](docs/imagens-clinicas.md) — fotos clínicas e antes/depois
- [docs/pacotes-tratamento.md](docs/pacotes-tratamento.md) — pacotes com N sessões
- [docs/antropometria.md](docs/antropometria.md) — antropometria e bioimpedância
- [docs/odontograma.md](docs/odontograma.md) — odontograma FDI
- [docs/escalas-testes.md](docs/escalas-testes.md) — escalas validadas (PHQ-9, etc.)

## Compliance e qualidade

- [docs/lgpd.md](docs/lgpd.md) — conformidade LGPD
- [docs/auditoria.md](docs/auditoria.md) — auditoria e trilha
- [docs/compliance-conselhos.md](docs/compliance-conselhos.md) — CFM, CFO, CRP, CRN, CREFITO, CRBM
- [docs/acessibilidade.md](docs/acessibilidade.md) — WCAG e a11y

## SaaS e operação

- [docs/billing.md](docs/billing.md) — billing e planos
- [docs/personalizacao.md](docs/personalizacao.md) — personalização por tenant
- [docs/importacao-exportacao.md](docs/importacao-exportacao.md) — import/export e portabilidade
- [docs/onboarding.md](docs/onboarding.md) — onboarding de clínica
- [docs/suporte.md](docs/suporte.md) — suporte ao cliente

## Fluxos canônicos ([examples/](examples/))

- [Agendamento](examples/fluxo-agendamento.md)
- [Prontuário](examples/fluxo-prontuario.md)
- [Financeiro](examples/fluxo-financeiro.md)
- [WhatsApp](examples/fluxo-whatsapp.md)
- [Laboratório](examples/fluxo-laboratorio.md)
- [Onboarding de clínica](examples/fluxo-onboarding.md)
- [Portal do paciente](examples/fluxo-portal-paciente.md)
- [MFA](examples/fluxo-mfa.md)
- [Pacote de tratamento](examples/fluxo-pacote-tratamento.md)
- [Plano alimentar](examples/fluxo-plano-alimentar.md)

## Decisões arquiteturais ([ADRs/](ADRs/))

1. [API-first](ADRs/001-api-first.md)
2. [Multi-tenancy row-based](ADRs/002-multi-tenant.md)
3. [PostgreSQL](ADRs/003-postgresql.md)
4. [Next.js](ADRs/004-nextjs.md)
5. [RBAC granular](ADRs/005-rbac.md)
6. [Event-driven](ADRs/006-event-driven.md)
7. [Storage com Cloudflare R2](ADRs/007-storage-cloudflare-r2.md)
8. [WhatsApp via Evolution API](ADRs/008-whatsapp-evolution-api.md)
9. [Pagamentos: Mercado Pago + Stripe](ADRs/009-pagamentos-mercadopago-stripe.md)
10. [Observabilidade com Sentry](ADRs/010-observabilidade-sentry.md)

---

# Roadmap

Construído em fases acionáveis, cada uma com critério de saída quantificado:

| # | Fase | Sprints | Duração | Marco final |
|---|------|---------|---------|-------------|
| Pré-0 | [Preparação](docs/roadmap/00-preparacao.md) | — | 2–3 sem | Design system, contas e ambiente local prontos |
| 0 | [Fundação](docs/roadmap/01-fundacao.md) | 01–02 | 5–7 sem | Multi-tenant + auth + agenda base |
| 1 | [MVP](docs/roadmap/02-mvp.md) | 03–04 | 6–8 sem | Vendável para qualquer das 8 especialidades |
| 1.5 | [Polimento](docs/roadmap/03-polimento.md) | 05–06 | 6 sem | Piloto estabilizado, Meta-aprovado |
| 2 | [Expansão](docs/roadmap/04-expansao.md) | 07–12 | 4–6 meses | 10+ clínicas pagantes |
| 2.5 | [Laboratório](docs/roadmap/05-laboratorio.md) | 13–15 | 2–3 meses | Laboratórios independentes em produção |
| 3 | [Inteligência (IA)](docs/roadmap/06-inteligencia.md) | 16–19 | 3–4 meses | IA com aceitação > 60% |
| 4 | [Mobile](docs/roadmap/07-mobile.md) | 20–23 | 3–4 meses | Apps publicados nas lojas |
| 5 | [Plataforma](docs/roadmap/08-plataforma.md) | 24–28 | contínuo | API pública + marketplace |
| 6 | [Enterprise](docs/roadmap/09-enterprise.md) | 29+ | sob demanda | SSO + redes/franquias |

Tracks paralelos sempre vivos:

- [Go-to-market](docs/roadmap/10-go-to-market.md) — vendas, piloto, growth, parcerias
- [Cadência operacional](docs/roadmap/11-cadencia-operacional.md) — code review, retros, segurança, on-call

**Total estimado até Fase 5:** ~24–30 meses.

Detalhamento completo no [master index do roadmap](docs/roadmap.md).

---

# Status e próximos passos

**Hoje:** documentação e arquitetura concluídas · zero código de produção · MVP totalmente especificado para as 8 especialidades

**Próximo passo:** executar [docs/roadmap/00-preparacao.md](docs/roadmap/00-preparacao.md) (Pré-Fase 0) — 2–3 semanas de design system, provisionamento de contas, ambiente local, contratos legais e identificação da clínica piloto.

Depois disso, [Sprint 01](tasks/sprint-01.md) começa.

---

# Princípios

## Produto

- **simplicidade operacional** > recursos demais
- **velocidade** > perfeição
- **UX premium** sem comprometer performance
- **multi-especialidade** desde o MVP via engine composable
- **sem aprisionar o cliente** — portabilidade total dos dados

## Engenharia

- **API-first** — todo backend acessível via REST versionada
- **multi-tenant obrigatório** — toda entidade considera tenant
- **LGPD + RBAC + auditoria** desde o dia 1
- **simplicidade > esperteza** — código legível antes de eficiente
- **DRY · KISS · SOLID · Separation of Concerns**
- **sem gambiarra, sem hardcode desnecessário**

## Operação

- **observabilidade** real (Sentry + logs JSON + `request_id`)
- **CI verde obrigatório** para merge
- **code review** sem exceção
- **postmortems** sem culpa
- **runbooks** atualizados após cada incidente

Detalhamento em [CLAUDE.md](CLAUDE.md) e [docs/roadmap/11-cadencia-operacional.md](docs/roadmap/11-cadencia-operacional.md).

---

# Como começar

> O projeto está em fase de planejamento. O código ainda não foi iniciado.

## Para entender o projeto

1. Leia [docs/vision.md](docs/vision.md) (visão estratégica)
2. Leia [docs/arquitetura.md](docs/arquitetura.md) (decisões arquiteturais macro)
3. Leia [docs/stack.md](docs/stack.md) (stack técnica)
4. Veja a [matriz do roadmap](docs/roadmap.md) para entender a jornada completa

## Para começar a codar (quando o tempo chegar)

1. Execute [docs/roadmap/00-preparacao.md](docs/roadmap/00-preparacao.md) — Pré-Fase 0
2. Execute [tasks/sprint-01.md](tasks/sprint-01.md) — Sprint 01: Fundação técnica
3. Execute [tasks/sprint-02.md](tasks/sprint-02.md) — Sprint 02: Auth + Pacientes + Agenda
4. ...e siga as sprints seguintes em ordem

## Convenções de desenvolvimento

- todo PR exige aprovação + CI verde
- testes obrigatórios (cobertura ≥ 70% em services)
- multi-tenancy validada em cada feature
- ADR para toda decisão arquitetural significativa
- documentação atualizada junto com o código

Detalhamento em [CLAUDE.md](CLAUDE.md).

---

# Licença e autoria

Projeto privado em desenvolvimento. Direitos reservados.

Construído por **Warley Ferraz** com auxílio do **Claude Code** (Anthropic) para acelerar planejamento e documentação.

Contato: warley.ferraz.wf@gmail.com

---

> *"Construir o Clinix em fases acionáveis, com tracks paralelos sempre vivos. Cada fase entrega valor isolado. Cada fase reduz risco. Cada fase abre a próxima."*
