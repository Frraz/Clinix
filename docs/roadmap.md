# Roadmap — Clinix

> Master index do roadmap de desenvolvimento do Clinix. Cada fase tem detalhamento próprio em `docs/roadmap/`.

---

# 1. ESTADO ATUAL

Em **2026-05-27**, o Clinix possui:

* **Documentação completa**: 43 docs, 10 ADRs, 7 tasks (sprints 01–04 + mvp + backlog + bugs), 10 examples
* **Zero código de produção**: pastas `backend/`, `frontend/`, `infrastructure/` contêm apenas `CLAUDE.md`
* **Plano comercial**: lista de leads regionais em `POSSÍVEIS CLIENTES.MD` (Rondon do Pará)
* **8 especialidades-alvo** definidas: medicina, odontologia, fisioterapia, psicologia, biomedicina, dermatologia, estética, nutrição

O roadmap a seguir cobre **do zero até plataforma enterprise**.

---

# 2. FILOSOFIA

O Clinix é construído em **fases acionáveis**, cada uma:

* entrega valor isolado
* reduz risco da próxima
* tem critério de saída quantificado
* não tenta resolver tudo de uma vez

E **acompanhada de tracks paralelos** que não param em nenhuma fase:

* **Go-to-market** — vendas, piloto, growth (`docs/roadmap/10-go-to-market.md`)
* **Cadência operacional** — code review, retros, segurança, dependências (`docs/roadmap/11-cadencia-operacional.md`)

---

# 3. PRINCÍPIOS DE PRIORIZAÇÃO

Toda decisão responde a:

1. **Destrava receita?** — venda, conversão, ticket
2. **Reduz churn?** — retenção, NPS, estabilidade
3. **Destrava próxima fase?** — pré-requisito real
4. **Atende dor medida?** — não palpite
5. **Custo proporcional ao valor?** — ROI claro
6. **Cobre as 8 especialidades?** — ou cria gap

---

# 4. TIMELINE MACRO

| # | Fase | Sprints | Duração | Marco final |
|---|------|---------|---------|-------------|
| Pré-0 | **Preparação** | — | 2–3 sem | Design system, contas, ambiente local prontos |
| 0 | **Fundação** | 01–02 | 5–7 sem | Base técnica multi-tenant + auth + agenda base |
| 1 | **MVP** | 03–04 | 6–8 sem | Vendável para qualquer das 8 especialidades |
| 1.5 | **Polimento** | 05–06 | 6 sem | Piloto estabilizado, NPS coletado, Meta-aprovado |
| 2 | **Expansão** | 07–12 | 4–6 meses | 10+ clínicas pagantes, MRR sustenta time |
| 2.5 | **Laboratório completo** | 13–15 | 2–3 meses | Laboratórios independentes em produção |
| 3 | **Inteligência (IA)** | 16–19 | 3–4 meses | IA clínica + operacional em produção, aceitação > 60% |
| 4 | **Mobile** | 20–23 | 3–4 meses | Apps Paciente e Profissional publicados nas lojas |
| 5 | **Plataforma** | 24–28 | contínuo | API pública, SDK, marketplace de integrações |
| 6 | **Enterprise** | 29+ | sob demanda | SSO, tenant dedicado, redes/franquias |

**Total estimado até Fase 5:** ~24–30 meses (2–2,5 anos).

---

# 5. ARQUIVOS DE DETALHE

Cada fase tem detalhamento próprio com sprints, entregáveis, riscos, KPIs e custos:

* [`docs/roadmap/00-preparacao.md`](roadmap/00-preparacao.md) — Pré-Fase 0
* [`docs/roadmap/01-fundacao.md`](roadmap/01-fundacao.md) — Fase 0
* [`docs/roadmap/02-mvp.md`](roadmap/02-mvp.md) — Fase 1
* [`docs/roadmap/03-polimento.md`](roadmap/03-polimento.md) — Fase 1.5
* [`docs/roadmap/04-expansao.md`](roadmap/04-expansao.md) — Fase 2
* [`docs/roadmap/05-laboratorio.md`](roadmap/05-laboratorio.md) — Fase 2.5
* [`docs/roadmap/06-inteligencia.md`](roadmap/06-inteligencia.md) — Fase 3
* [`docs/roadmap/07-mobile.md`](roadmap/07-mobile.md) — Fase 4
* [`docs/roadmap/08-plataforma.md`](roadmap/08-plataforma.md) — Fase 5
* [`docs/roadmap/09-enterprise.md`](roadmap/09-enterprise.md) — Fase 6
* [`docs/roadmap/10-go-to-market.md`](roadmap/10-go-to-market.md) — Track paralelo de negócio
* [`docs/roadmap/11-cadencia-operacional.md`](roadmap/11-cadencia-operacional.md) — Track paralelo de operação

---

# 6. DEPENDÊNCIAS ENTRE FASES

```txt
Pré-0  →  0  →  1  →  1.5  →  2  →  2.5
                          ↘   ↘    
                           3       
                          ↘   ↘    
                           4 → 5 → 6
```

* **Pré-0 destrava 0**: sem design system + contas + ambiente, Sprint 01 trava
* **0 destrava 1**: sem multi-tenancy + auth + design system, módulos não existem
* **1 destrava 1.5**: piloto real precisa estar em produção
* **1.5 destrava 2**: feedback do piloto direciona expansão
* **2 destrava 2.5**: laboratório precisa de convênios para faturamento
* **2 destrava 3**: IA precisa de dados reais e templates estáveis
* **3 destrava 4**: mobile prefere IA já validada para diferenciar
* **4 destrava 5**: plataforma assume operação multi-cliente madura
* **5 destrava 6**: enterprise pressupõe APIs públicas e integrações

---

# 7. SPRINTS PLANEJADAS

| Sprint | Fase | Foco | Duração | Arquivo |
|--------|------|------|---------|---------|
| 01 | 0 | Fundação técnica | 2–3 sem | `tasks/sprint-01.md` |
| 02 | 0 | Auth + Pacientes + Agenda base | 3–4 sem | `tasks/sprint-02.md` |
| 03 | 1 | Prontuário composable + Financeiro essencial | 3–4 sem | `tasks/sprint-03.md` |
| 04 | 1 | WhatsApp + Portal + componentes por especialidade | 3–4 sem | `tasks/sprint-04.md` |
| 05 | 1.5 | Bugs do piloto + performance + UX refinements | 3 sem | `tasks/sprint-05.md` |
| 06 | 1.5 | LGPD hardening + Meta-approval + Portal v2 | 3 sem | `tasks/sprint-06.md` |
| 07 | 2 | CRM pipeline + Multi-unidade | 4 sem | `tasks/sprint-07.md` |
| 08 | 2 | Convênios + TISS básico + Estoque | 4 sem | `tasks/sprint-08.md` |
| 09–12 | 2 | Automações low-code + ICP-Brasil + Domínio próprio + Import API | 4×4 sem | (a planejar) |
| 13–15 | 2.5 | Laboratório completo | 3×4 sem | (a planejar) |
| 16–19 | 3 | IA clínica/operacional/conversacional | 4×3 sem | (a planejar) |
| 20–23 | 4 | App Paciente + App Profissional | 4×3 sem | (a planejar) |
| 24–28 | 5 | API pública + Marketplace + Integrações | contínuo | (a planejar) |
| 29+ | 6 | SSO + Enterprise | sob demanda | (a planejar) |

Sprints 01–08 já existem como arquivos. Demais serão criados quando entrarem no horizonte de 2 sprints.

---

# 8. CAPACIDADE DE TIME

| Fase | Mínimo viável | Confortável | Acelerado |
|------|---------------|-------------|-----------|
| Pré-0 | 1 dev fullstack + designer | + PM | + designer dedicado |
| 0 | 1 dev fullstack | 1 backend + 1 frontend | + 1 devops |
| 1 | 2 devs | 1 BE + 1 FE + 1 designer | + 1 QA |
| 1.5 | 2 devs | + CS dedicado | + suporte 24/7 |
| 2 | 3 devs | + 1 product manager | + 1 QA + analista |
| 2.5 | 3 devs | + especialista lab consultoria | + 1 dev sênior |
| 3 | 3 devs + ML eng | + dados/ML eng dedicado | + IA eng |
| 4 | 1 dev mobile RN | + 1 fullstack ajudando | + 2 mobile devs |
| 5 | 3 devs | + 1 DevRel | + 1 SDK developer |
| 6 | 4+ devs | + SRE | time enterprise dedicado |

O Clinix nasce com **1 dev fullstack + designer freelance**. Crescimento de time acompanha receita.

---

# 9. CUSTOS DE INFRA E SERVIÇOS (USD ou BRL — valores indicativos)

| Serviço | MVP | Fase 2 | Fase 3+ |
|---------|-----|--------|---------|
| Hostinger VPS (Brasil) | R$ 60–150/mês | R$ 300/mês | R$ 600+/mês |
| Cloudflare (CDN/DNS/R2) | gratuito | R$ 100/mês storage | R$ 500+/mês |
| Sentry | gratuito (5k/mês) | $26/mês | $80+/mês |
| Resend | gratuito (3k/mês) | $20/mês | $100/mês |
| Evolution API (VPS extra) | R$ 60/mês | R$ 150/mês | R$ 300/mês |
| Mercado Pago | comissão | comissão | comissão |
| Stripe | comissão | comissão | comissão |
| OpenAI/Anthropic API | — | — | $200–2000/mês |
| Domain + SSL | R$ 50/ano | + subdomínios | + multi-domínio |
| **Subtotal mensal** | **~R$ 200** | **~R$ 800** | **~R$ 2500+** |

Custo unitário diluído: ~R$ 20–50/clínica/mês em escala (excluindo comissões).

---

# 10. KPIs DE CADA FASE

| Fase | KPI principal | Meta |
|------|---------------|------|
| Pré-0 | Design system completo | 100% das telas críticas com mockup aprovado |
| 0 | CI verde + staging up | deploy em < 5min |
| 1 | Clínica piloto em produção | 30 dias sem incidente crítico |
| 1.5 | NPS + redução de trabalho manual | NPS > 8, redução de tempo medida |
| 2 | Clínicas pagantes | 10+ pagantes, churn < 5%/mês |
| 2.5 | Laboratórios em produção | 2+ labs operando completo |
| 3 | Aceitação de IA | > 60% das sugestões aceitas |
| 4 | Adoção mobile | > 40% dos pacientes ativos com app |
| 5 | Integrações | 5+ integrações no marketplace |
| 6 | Receita enterprise | primeiro contrato > R$ 5k/mês |

---

# 11. ANTI-METAS GLOBAIS

NÃO faremos nunca (até decisão estratégica explícita):

* hospital management (alta complexidade, mercado diferente)
* prontuário SUS (regulação separada, baixo ticket)
* integração com TODOS os convênios (apenas os principais regionais)
* IA generativa de diagnóstico autônomo (regulação delicada)
* atendimento internacional (foco regional primeiro)
* mineração de dados de pacientes (LGPD + ética)

---

# 12. REVISÃO DO ROADMAP

Atualizado:

* **a cada fase concluída** — ajusta as próximas com base no aprendido
* **a cada trimestre** — mesmo sem fase nova
* **sempre que houver mudança estratégica** (novo mercado, novo investidor, etc.)

Mudanças não-triviais geram **ADR de revisão** com justificativa.

---

# 13. COMO USAR ESTE ROADMAP

* **Time técnico**: começa em `docs/roadmap/00-preparacao.md` e avança fase a fase
* **CEO/Founder**: lê este index + `docs/roadmap/10-go-to-market.md` para acompanhar valor
* **CS/Suporte**: foca em `docs/roadmap/11-cadencia-operacional.md` + fases de polimento
* **Investidor/Stakeholder**: tabela seção 4 + KPIs seção 10
* **Designer**: começa em `docs/roadmap/00-preparacao.md`, depois acompanha entregáveis UX
* **Novo membro**: lê este index + a fase atual + sprint atual

---

# 14. OBJETIVO FINAL

Construir o Clinix em **fases acionáveis, com tracks paralelos sempre vivos**:

* cada fase entrega valor isolado
* cada fase reduz risco
* cada fase abre a próxima
* nenhuma fase tenta resolver tudo
* go-to-market e operação nunca pausam
* o conjunto se torna a plataforma operacional mais completa do mercado regional brasileiro

E preparado para escalar nacionalmente quando o tempo, capital e produto permitirem.

---

# 15. REFERÊNCIAS

* `docs/vision.md` — visão estratégica
* `docs/funcionalidades.md` — mapa completo de funcionalidades
* `docs/stack.md` — stack técnica
* `tasks/mvp.md` — definição do MVP
* `tasks/backlog.md` — backlog priorizado
* `ADRs/` — decisões arquiteturais
* `examples/` — fluxos canônicos
* `POSSÍVEIS CLIENTES.MD` — leads iniciais (Rondon do Pará)
