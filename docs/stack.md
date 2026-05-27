# Clinix — Stack Técnica Completa

> Documento de referência para o Claude Code e para o time de desenvolvimento.
> Todas as decisões tecnológicas do projeto Clinix estão documentadas aqui,
> organizadas por camada, com justificativa de cada escolha.

---

## Visão geral da arquitetura

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENTES                                  │
│   Browser (Next.js)  │  App iOS/Android (React Native / Expo)   │
└────────────┬─────────────────────────┬───────────────────────────┘
             │ HTTPS / REST            │ HTTPS / REST + Push
             ▼                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                     NGINX (reverse proxy)                        │
│              SSL termination · static files · gzip               │
└────────┬─────────────────────────────────────┬───────────────────┘
         │                                     │
         ▼                                     ▼
┌─────────────────┐                 ┌──────────────────┐
│   Gunicorn      │                 │     Daphne       │
│  (WSGI / API)   │                 │  (ASGI / WS)     │
└────────┬────────┘                 └────────┬─────────┘
         │                                   │
         ▼                                   ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DJANGO 5 (monorepo modular)                   │
│  core · patients · scheduling · records · financial · lab        │
│  communications · automation · analytics · billing               │
└──────┬──────────────────┬───────────────────┬────────────────────┘
       │                  │                   │
       ▼                  ▼                   ▼
┌────────────┐   ┌──────────────┐   ┌──────────────────┐
│ PostgreSQL │   │    Redis     │   │  Celery Workers  │
│    16      │   │  (cache +    │   │  (tasks async)   │
│  (dados)   │   │   broker)    │   │                  │
└────────────┘   └──────────────┘   └──────────────────┘
       │
       ▼
┌──────────────────────────────────────────────────┐
│             Cloudflare R2 (storage S3)           │
│    laudos · imagens · documentos · backups       │
└──────────────────────────────────────────────────┘
```

**Princípio central: API-first.**
Um único backend Django serve o web (Next.js) e o mobile (React Native) sem duplicação de lógica.

---

## Camada 1 — Backend (Python / Django)

### Framework principal

| Tecnologia | Versão | Função |
|---|---|---|
| Python | 3.12+ | Linguagem base |
| Django | 5.x | Framework web principal |
| Django REST Framework | 3.15+ | API REST robusta |
| Gunicorn | latest | Servidor WSGI para produção |
| Daphne | latest | Servidor ASGI para WebSockets |

**Por que Django:**
- ORM poderoso, ideal para modelagem de entidades médicas complexas.
- Admin gratuito útil para operações internas do SaaS.
- Ecossistema maduro de pacotes para saúde, segurança e SaaS.
- Preparado nativamente para background jobs, WebSockets e API REST.
- A mesma API serve web e mobile sem nenhuma duplicação.

### Autenticação e permissões

| Tecnologia | Função |
|---|---|
| `djangorestframework-simplejwt` | JWT com access + refresh tokens, rotação automática, blacklist |
| `django-guardian` | Permissões granulares por objeto (RBAC avançado por prontuário, unidade, paciente) |
| `django-otp` | MFA com TOTP (Google Authenticator), SMS OTP, backup codes |
| `django-cors-headers` | Política de CORS para frontend separado e app mobile |

**Fluxo de autenticação:**
```
Login → JWT access token (15min) + refresh token (30 dias)
Refresh token → novo access token (rotação automática)
MFA → TOTP obrigatório para perfis médico e administrador
RBAC → permissões por módulo, por unidade, por objeto
```

### Multi-tenancy (SaaS)

**Estratégia oficial: row-based multi-tenancy.**

| Componente | Função |
|---|---|
| Middleware customizado | Resolve `tenant` por JWT/subdomínio e injeta em `request.tenant` |
| `TenantAwareManager` | Manager base que filtra automaticamente por `tenant_id` em toda query |
| `TenantAwareModel` | Abstract base model com `tenant_id` FK, `created_at`, `updated_at` |
| Middleware de auditoria | Bloqueia queries cross-tenant em desenvolvimento (assertion) |

**Por que row-based (e não schema-based):**
- Simplicidade operacional: um único schema, migrations únicas, sem N schemas para versionar.
- Backups, analytics cross-tenant (uso do SaaS) e relatórios consolidados ficam triviais.
- Menor custo de infraestrutura: o connection pool não explode com centenas de tenants.
- Migração futura para schema-based ou DB dedicado é possível sem reescrita — basta um campo extra de roteamento.
- Reduz risco de bugs de tooling: PgAdmin, dumps, índices funcionam normalmente.

**Mitigação do risco de vazamento (essencial em row-based):**
- `TenantAwareManager` padrão impede `.objects.all()` sem contexto.
- Testes obrigatórios de isolamento entre tenants no CI (`pytest -m tenancy`).
- Code review obrigatório em qualquer query que use `.objects.filter()` sem `tenant=`.
- Row-level security do PostgreSQL como camada extra para tabelas críticas (`medical_records`, `prescriptions`).

**Estrutura de subdomínios:**
```
clinix.com.br          → landing page / marketing
app.clinix.com.br      → painel super-admin SaaS (cross-tenant)
{slug}.clinix.com.br   → instância da clínica (resolve tenant_id)
```

### Apps Django (estrutura modular)

```
backend/apps/
├── core/           # tenant, auth, RBAC, audit, notificações base
├── pacientes/      # cadastro, timeline, LGPD, dependentes, convênios
├── agenda/         # consultas, status, check-in, fila de espera
├── prontuario/     # registros clínicos, templates, builder, assinatura
├── financeiro/     # contas, pagamentos, TISS, repasse, DRE
├── laboratorio/    # exames, coleta, laudos, rastreamento
├── comunicacao/    # WhatsApp, e-mail, SMS, omnichannel
├── automacoes/     # motor de workflows, Celery tasks, gatilhos
├── analytics/      # BI, relatórios, KPIs, dashboards
├── billing/        # planos SaaS, trials, upgrade/downgrade
├── crm/            # leads, campanhas, pipeline, recuperação
└── api/            # roteamento central de endpoints REST
```

**Convenção:** nomes de apps em português (`pacientes`, `agenda`) para alinhar com o domínio e a documentação. Models e variáveis em inglês (`Patient`, `Appointment`).

---

## Camada 2 — Banco de dados e Cache

### Banco de dados principal

| Tecnologia | Versão | Função |
|---|---|---|
| PostgreSQL | 16 | Banco de dados relacional principal |

**Por que PostgreSQL:**
- JSONB nativo para prontuário dinâmico (campos customizáveis por especialidade).
- Full-text search nativo em português (busca de pacientes, CID, medicamentos).
- Row-level security para camada extra de isolamento por tenant.
- Particionamento de tabelas para histórico de eventos (auditoria, timeline).
- Suporte nativo a arrays, UUID e tipos avançados.
- `pg_trgm` para busca fuzzy (nome do paciente com erro de digitação).

**Campos JSONB usados em:**
- `records.MedicalRecord.content` — conteúdo dinâmico do prontuário por especialidade.
- `automation.Workflow.config` — configuração do motor de automações.
- `billing.Plan.features` — features habilitadas por plano.

### Cache e filas

| Tecnologia | Versão | Função |
|---|---|---|
| Redis | 7 | Cache de sessões, broker Celery, pub/sub para Channels |
| Celery | 5 | Background jobs assíncronos |
| Celery Beat | — | Scheduler de tarefas periódicas |

**Tarefas Celery (exemplos):**
```python
# Tarefas imediatas (queue: default)
send_whatsapp_message.delay(patient_id, template, params)
generate_pdf_report.delay(report_id)
send_lab_result_email.delay(result_id)

# Tarefas periódicas (Celery Beat)
@periodic_task(run_every=crontab(hour=8, minute=0))  # diário às 8h
def send_daily_appointment_reminders(): ...

@periodic_task(run_every=crontab(day_of_month=1))   # mensal
def generate_monthly_financial_report(): ...

@periodic_task(run_every=crontab(minute='*/15'))     # a cada 15 min
def check_no_show_appointments(): ...
```

---

## Camada 3 — Frontend Web

### Framework e linguagem

| Tecnologia | Versão | Função |
|---|---|---|
| Next.js | 15 | Framework React com App Router, SSR, RSC |
| TypeScript | 5 | Tipagem estática obrigatória |
| React | 19 | Biblioteca de UI (via Next.js) |

**Por que Next.js (e não Django Templates):**
- Sistema de saúde exige UX extremamente refinada — Templates não escalam para isso.
- App Router e React Server Components entregam performance extrema.
- SSR melhora o tempo de carregamento inicial das telas críticas.
- O mesmo ecossistema React é reutilizado no app mobile (React Native).
- TypeScript elimina bugs de runtime em dados clínicos críticos.

**Estrutura de pastas Next.js:**
```
web/
├── app/
│   ├── (auth)/           # login, MFA, recuperação de senha
│   ├── (dashboard)/      # área principal da clínica
│   │   ├── agenda/       # calendário e atendimentos
│   │   ├── pacientes/    # lista e perfil de pacientes
│   │   ├── prontuario/   # editor clínico
│   │   ├── financeiro/   # contas e relatórios
│   │   ├── laboratorio/  # exames e laudos
│   │   └── relatorios/   # BI e dashboards
│   └── (portal)/         # portal público do paciente
├── components/
│   ├── ui/               # shadcn/ui customizados
│   ├── agenda/           # componentes de agenda
│   ├── prontuario/       # editor clínico
│   └── shared/           # componentes reutilizáveis
├── lib/
│   ├── api/              # funções de fetch para o Django
│   ├── hooks/            # custom hooks React
│   └── types/            # tipos TypeScript compartilhados
└── stores/               # Zustand stores globais
```

### Estilização e componentes

| Tecnologia | Função |
|---|---|
| Tailwind CSS 4 | Framework CSS utility-first, dark mode automático |
| shadcn/ui | Componentes acessíveis sem vendor lock-in, 100% customizáveis |
| Lucide React | Ícones consistentes e leves |

### Gerenciamento de estado e dados

| Tecnologia | Função |
|---|---|
| TanStack Query v5 | Server state, cache automático, refetch, optimistic updates |
| Zustand | Estado global leve (paciente ativo, modal, perfil do usuário) |

**Padrão de fetch:**
```typescript
// TanStack Query para dados do servidor
const { data: patient } = useQuery({
  queryKey: ['patient', patientId],
  queryFn: () => api.patients.get(patientId),
  staleTime: 1000 * 60 * 5, // 5 minutos de cache
})

// Zustand para estado de UI global
const { activePatient, setActivePatient } = useClinicStore()
```

### Componentes de domínio clínico

| Tecnologia | Função |
|---|---|
| FullCalendar React | Agenda com drag-and-drop, múltiplas visualizações, recorrência |
| TipTap | Editor rich text para prontuário, templates SOAP, campos dinâmicos |
| Recharts | Gráficos de faturamento, no-show, produtividade e KPIs |
| React Hook Form + Zod | Formulários clínicos com validação tipada e schema compartilhado |

---

## Camada 4 — App Mobile (Fase 2)

| Tecnologia | Função |
|---|---|
| React Native | Framework mobile iOS + Android com um único código |
| Expo SDK 52+ | Tooling: build nativo sem Xcode local, câmera, biometria, OTA updates |
| Expo Router | Navegação file-based (mesmo padrão do Next.js) |
| Expo Notifications + FCM | Push notifications iOS e Android via Firebase Cloud Messaging |
| React Native MMKV | Storage local seguro e ultrarrápido para tokens e preferências |
| TanStack Query v5 | Mesmo padrão de fetch do frontend web — reutilização máxima |

**Por que React Native + Expo:**
- Compartilha toda a lógica de negócio e tipos TypeScript com o web.
- O backend Django já está pronto (API-first) — zero esforço adicional.
- EAS Build (Expo Application Services) faz build na nuvem sem Mac.
- OTA updates: corrigi um bug, o app atualiza sem passar pela loja.
- Um desenvolvedor cobre iOS e Android simultaneamente.

**Três apps planejados:**
```
App Profissional  → agenda, prontuário, evolução, telemedicina, alertas
App Paciente      → agendamento, exames, histórico, pagamentos, chat
App Gestor        → KPIs, financeiro, indicadores por unidade
```

---

## Camada 5 — Integrações e Comunicação

### Comunicação

| Tecnologia | Função | Custo |
|---|---|---|
| Evolution API | WhatsApp Business API, self-hosted | Grátis (self-hosted na VPS) |
| Resend | E-mail transacional com React Email templates | Grátis até 3k/mês |
| React Email | Templates de e-mail em React | Grátis |

**WhatsApp — casos de uso automatizados:**
```
Confirmação de consulta    → 48h antes do agendamento
Lembrete de consulta       → 24h e 2h antes
Pós-consulta               → 2h após o atendimento
Resultado de exame         → ao liberar o laudo
Cobrança de inadimplência  → D+3, D+7, D+15 do vencimento
Recuperação de faltosos    → 2h após o no-show
Solicitação de retorno     → 30/60/90 dias após a última consulta
```

### Pagamentos e Billing

| Tecnologia | Função |
|---|---|
| Mercado Pago | PIX, boleto, cartão de crédito para pacientes — melhor para BR |
| Stripe | Assinaturas recorrentes do SaaS (planos das clínicas) |

### Geração de documentos

| Tecnologia | Função |
|---|---|
| WeasyPrint | HTML/CSS → PDF de alta qualidade para laudos, receitas, atestados |
| Pillow | Processamento de imagens (fotos de pacientes, imagens clínicas) |

### Storage de arquivos

| Tecnologia | Função |
|---|---|
| Cloudflare R2 | Storage S3-compatible com egress gratuito |
| `django-storages` | Integração transparente do Django com R2 |

**Arquivos armazenados no R2:**
- Laudos em PDF
- Imagens clínicas (odontológicas, dermatológicas)
- Fotos de pacientes
- Documentos assinados (contratos, termos)
- Backups do banco de dados

### APIs brasileiras

| Tecnologia | Função |
|---|---|
| ViaCEP API | Autopreenchimento de endereço por CEP |
| Receita WS | Validação e dados de CNPJ para cadastro de clínicas |
| IBGE API | Listagem de cidades e estados |

---

## Camada 6 — Segurança e Compliance

### Pacotes de segurança

| Tecnologia | Função |
|---|---|
| `django-otp` | MFA com TOTP (Google Authenticator), SMS OTP, backup codes |
| `django-auditlog` | Registro imutável de toda alteração em modelos críticos |
| `django-encrypted-model-fields` | Criptografia AES-256 de campos sensíveis no banco |
| `django-ratelimit` | Rate limiting de API — anti brute-force e anti-spam |
| `django-cors-headers` | Política de CORS configurável por ambiente |

### Requisitos de segurança implementados

```
✓ JWT com rotação de refresh token
✓ MFA obrigatório para médicos e administradores
✓ RBAC granular por módulo, unidade e objeto
✓ Audit log imutável (quem, o quê, quando, de onde)
✓ Criptografia AES-256 em campos sensíveis (CPF, prontuário)
✓ HTTPS obrigatório em todos os ambientes
✓ Rate limiting em todos os endpoints públicos
✓ Headers de segurança (HSTS, CSP, X-Frame-Options)
✓ Sanitização de inputs contra XSS e SQL Injection
✓ Backup criptografado diário com retenção de 30 dias
```

### Conformidade LGPD

Implementação própria (sem biblioteca externa) seguindo os artigos 7, 8, 18 e 46 da LGPD:

```python
# Modelos necessários para conformidade
ConsentRecord      # registro de consentimentos por finalidade
DataProcessingLog  # log de acesso a dados pessoais sensíveis
AnonymizationJob   # tarefa de anonimização sob demanda
DeletionRequest    # solicitação de exclusão (direito ao esquecimento)
PrivacyPolicy      # versões da política de privacidade aceitas
```

---

## Camada 7 — Infraestrutura e DevOps

### Stack de deploy na Hostinger VPS

| Tecnologia | Função | Custo estimado |
|---|---|---|
| Hostinger VPS KVM 4 | Servidor principal de produção (4 vCPU, 16GB RAM, 200GB NVMe) | ~R$150/mês |
| Hostinger VPS KVM 2 | Servidor de staging (2 vCPU, 8GB RAM) | ~R$70/mês |
| Docker + Docker Compose | Containerização de todos os serviços | Grátis |
| Nginx | Reverse proxy, static files, SSL termination, rate limiting | Grátis |
| Gunicorn | Servidor WSGI para a API Django síncrona | Grátis |
| Daphne | Servidor ASGI para WebSockets (Django Channels) | Grátis |
| Cloudflare | CDN, DDoS, DNS, SSL gratuito, firewall rules | Grátis |
| Cloudflare R2 | Storage de arquivos (egress gratuito) | ~$0.015/GB/mês |
| GitHub Actions | CI/CD: testes, build, deploy automático | Grátis (2k min/mês) |
| Sentry | Monitoramento de erros em tempo real | Grátis (5k erros/mês) |
| Flower | Dashboard de monitoramento do Celery | Grátis (self-hosted) |

**Custo total de infraestrutura no MVP: ~R$250–350/mês.**
Um único cliente no plano Professional (R$397/mês) já cobre toda a infra.

### docker-compose.yml (serviços)

```yaml
services:
  nginx:          # reverse proxy e static files
  web:            # Django via Gunicorn (API REST)
  daphne:         # Django Channels via Daphne (WebSockets)
  celery:         # worker de tarefas assíncronas
  celery-beat:    # scheduler de tarefas periódicas
  flower:         # monitor de tarefas Celery (porta interna)
  postgres:       # banco de dados PostgreSQL
  redis:          # cache e broker de mensagens
  evolution-api:  # WhatsApp Business API self-hosted
```

### CI/CD com GitHub Actions

```
Push para main →
  1. Lint (Ruff, ESLint)
  2. Testes (pytest-django, Vitest)
  3. Build da imagem Docker
  4. Push para GitHub Container Registry
  5. Deploy via SSH na VPS (docker compose pull + up)
  6. Health check automático
  7. Rollback automático se health check falhar
```

---

## Camada 8 — Testes e Qualidade de Código

### Backend

| Tecnologia | Função |
|---|---|
| `pytest-django` | Testes unitários e de integração — mais flexível que unittest |
| `factory_boy` | Factories para geração de dados de teste realistas |
| `Faker` | Dados fictícios em português (nome, CPF, endereço, CRM) |
| `coverage.py` | Cobertura de código — meta mínima de 80% nos módulos críticos |

### Frontend

| Tecnologia | Função |
|---|---|
| Vitest | Testes unitários ultrarrápidos (Vite-native) |
| Testing Library | Testa comportamento real do usuário nos componentes |
| Playwright | Testes E2E dos fluxos críticos (agendamento, atendimento, pagamento) |
| MSW (Mock Service Worker) | Mock de API nas suítes de testes de componentes |

### Linting e formatação

| Tecnologia | Função |
|---|---|
| Ruff | Linter Python ultrarrápido (substitui flake8 + isort + pylint) |
| Black | Formatação automática de código Python |
| ESLint | Linter TypeScript/React |
| Prettier | Formatação automática de TypeScript/CSS |
| pre-commit | Hooks que rodam lint e formatação antes de cada commit |

---

## Camada 9 — Inteligência Artificial (Fase 3)

| Tecnologia | Função |
|---|---|
| OpenAI API / Claude API | Resumo de prontuário, sugestão de CID, preenchimento automático |
| LangChain | Agentes com memória, RAG sobre histórico clínico, chatbot contextualizado |
| scikit-learn | Previsão de no-show, inadimplência, demanda por horário |

**Casos de uso de IA planejados:**
```
Fase 3a — IA Clínica
  • Resumo automático do prontuário antes da consulta
  • Sugestão de CID-10 baseada na evolução clínica
  • Pré-preenchimento de prescrições recorrentes

Fase 3b — IA Operacional
  • Previsão de no-show (reduzir ociosidade da agenda)
  • Previsão de inadimplência (acionar cobrança preventiva)
  • Análise de gargalos operacionais

Fase 3c — IA Conversacional
  • Assistente interno para recepção ("quais pacientes chegaram?")
  • Chatbot no portal do paciente para dúvidas frequentes
  • Busca inteligente por histórico clínico
```

---

## Resumo executivo — todas as tecnologias

### Obrigatórias desde o dia 1 (Fase 1 — MVP)

```
Backend:     Django 5 · DRF · SimpleJWT · django-guardian
             django-otp · django-auditlog · django-encrypted-model-fields
             django-ratelimit · django-cors-headers
             Middleware/Manager customizado de tenancy (row-based)

Banco:       PostgreSQL 16 · Redis 7 · Celery 5 · Celery Beat

Frontend:    Next.js 15 · TypeScript 5 · Tailwind CSS 4 · shadcn/ui
             TanStack Query v5 · Zustand · React Hook Form + Zod
             FullCalendar · TipTap · Recharts

Integrações: Evolution API (WhatsApp) · Mercado Pago · Stripe
             Resend + React Email · WeasyPrint · django-storages + R2
             ViaCEP · Receita WS

Infra:       Docker Compose · Nginx · Gunicorn · Daphne
             GitHub Actions · Cloudflare · Sentry · Flower
             Hostinger VPS KVM 4 · Cloudflare R2

Testes:      pytest-django · factory_boy · Faker · coverage.py
             Vitest · Testing Library · Playwright · MSW
             Ruff · Black · ESLint · Prettier · pre-commit
```

### Fase 2 — Expansão (após MVP validado)

```
Mobile:      React Native · Expo SDK 52+ · Expo Router
             Expo Notifications · FCM · React Native MMKV
```

### Fase 3 — Diferenciação avançada

```
IA:          OpenAI API / Claude API · LangChain · scikit-learn
```

---

## Decisões de arquitetura importantes

### 1. Por que monorepo modular (não microsserviços)?

Microsserviços são overkill para um MVP e uma equipe pequena. Trazem complexidade de deployment, observabilidade e comunicação entre serviços sem benefício real até a escala de centenas de clínicas. O monorepo Django modular permite refatorar para serviços separados no futuro sem reescrever nada — cada `app/` Django já é uma unidade coesa.

### 2. Por que row-based multi-tenancy (e não schema-based)?

Schema-based (`django-tenants`) garante isolamento de banco perfeito, mas tem custos altos: cada tenant exige rodar migrations N vezes (uma por schema), o connection pool sofre quando há centenas de tenants, e queries cross-tenant para analytics, billing e operações do super-admin viram dor de cabeça. O risco real do row-based — vazamento por filtro esquecido — é mitigado por `TenantAwareManager` que impede `.objects.all()` sem contexto, testes obrigatórios de isolamento e PostgreSQL Row-Level Security nas tabelas críticas. Esta arquitetura também permite migrar para schema-based ou DB dedicado por enterprise client no futuro, sem reescrita.

### 3. Por que Next.js separado do Django (e não Django Templates)?

Um sistema de saúde exige UX extremamente refinada — agenda com drag-and-drop, prontuário com editor rich text, dashboards interativos, realtime. Django Templates não suportam esse nível de interatividade sem virar um spaghetti de JavaScript. Com Next.js separado, o mesmo frontend serve como base para o app mobile via React Native.

### 4. Por que Evolution API (e não Twilio/360Dialog)?

Evolution API é self-hosted, open-source e suporta a API oficial do WhatsApp Business. Custo zero de licença. Roda na mesma VPS. Twilio custaria ~$0.05 por mensagem — com 1.000 mensagens/mês por clínica, isso seria inviável. Evolution API elimina esse custo completamente.

### 5. Por que Cloudflare R2 (e não AWS S3)?

R2 é S3-compatible (mesma API, zero refatoração futura) mas tem egress de dados 100% gratuito. S3 cobra por transferência de dados — laudos e imagens médicas são baixados frequentemente, o que tornaria S3 caro. R2 elimina esse custo.

---

*Documento gerado em: maio de 2026*
*Projeto: Clinix — Plataforma Operacional de Saúde*
*Para uso com Claude Code e documentação interna do projeto*
