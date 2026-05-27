# Pré-Fase 0 — Preparação

> Trabalho preparatório que precisa estar pronto **antes** de o Sprint 01 começar. Sem isso, a fundação técnica trava.

---

# 1. OBJETIVO

Deixar todo o ecossistema pronto para começar a codar com confiança:

* identidade visual e design system aprovados
* contas e ambientes externos provisionados
* contratos e regras de negócio cristalizadas
* ambiente de desenvolvimento local funcionando
* pré-requisitos legais e fiscais resolvidos

Sem código de produção ainda. Apenas fundação.

---

# 2. PRÉ-REQUISITOS

* documentação completa em `docs/`, `ADRs/`, `tasks/`, `examples/` (já existe ✅)
* decisão estratégica das 8 especialidades-alvo (já existe ✅)
* lista de leads regionais validada (`POSSÍVEIS CLIENTES.MD` ✅)
* recursos financeiros para sustentar Pré-Fase + Fase 0 (2–3 meses de runway)

---

# 3. DURAÇÃO ESTIMADA

**2–3 semanas** com 1 dev fullstack + 1 designer freelance.

Pode estender para 4 semanas se houver iteração com piloto antes de começar.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Dev fullstack (founder) | 1 | 1 |
| Designer (UX/UI) | freelance | 1 dedicado |
| Product manager | — | parcial |
| Contador / consultor LGPD | 1× consulta | 1 retainer |

---

# 5. ESCOPO

## 5.1 Design system e identidade visual

### Entregáveis

* [ ] logo final em SVG (versão completa + ícone + reduzida)
* [ ] paleta de cores definida (primária, secundária, neutros, semânticos)
* [ ] tipografia escolhida (Inter ou similar; web font self-hosted)
* [ ] design tokens em JSON (cores, espaçamentos, raios, sombras)
* [ ] sistema de espaçamento (4/8/12/16/24/32/48/64)
* [ ] documentação do design system no Figma com biblioteca compartilhada
* [ ] componentes base no Figma: button, input, select, modal, table, card, tab, sidebar, header, empty state, skeleton, toast
* [ ] componentes especiais: agenda (FullCalendar mockado), prontuário (TipTap mockado), odontograma, mapa corporal, slider antes/depois, editor de plano alimentar
* [ ] wireframes das telas críticas:
  * [ ] login + MFA
  * [ ] dashboard "meu dia"
  * [ ] lista de pacientes + perfil
  * [ ] agenda diária/semanal/mensal
  * [ ] prontuário por especialidade (medicina, odonto, derm, estética, nutri)
  * [ ] financeiro do dia
  * [ ] portal do paciente
  * [ ] settings da clínica
* [ ] aprovação do founder em todos os mockups críticos

Detalhes em `docs/design-system.md`.

---

## 5.2 Contas e provisionamento externo

### Hosting e infra

* [ ] conta Hostinger VPS provisionada (plano com 4GB+ RAM)
* [ ] conta Cloudflare (DNS, CDN, R2, WAF) ativada
* [ ] domínio principal registrado (`clinix.app` ou similar)
* [ ] domínio reservado para portal (`portal.clinix.app`)
* [ ] subdomínios planejados (`api.`, `staging.`, `app.`)
* [ ] SSL via Cloudflare Origin Cert
* [ ] R2 buckets criados (`clinix-prod-files`, `clinix-prod-backups`, `clinix-prod-public`)

### Pagamentos

* [ ] conta Mercado Pago (modo developer + produção em paralelo)
* [ ] conta Stripe (modo test + produção em paralelo)
* [ ] webhooks endpoints reservados
* [ ] taxas e prazos documentados internamente

### Comunicação

* [ ] VPS dedicada para Evolution API (separada da principal)
* [ ] número WhatsApp Business adquirido (chip dedicado)
* [ ] conta Meta Business Manager
* [ ] conta Resend para email transacional

### Observabilidade

* [ ] conta Sentry (Django + Next.js + RN no futuro)
* [ ] DSNs configurados em cofre (1Password ou equivalente)

### Outros

* [ ] conta GitHub (repositório privado)
* [ ] conta de gerenciamento de tasks (Linear/Jira/Notion)
* [ ] canal de comunicação (Slack/Discord)
* [ ] 1Password Business para segredos compartilhados
* [ ] conta de monitoramento (UptimeRobot ou similar para healthchecks)

---

## 5.3 Ambiente de desenvolvimento local

### Setup que deve estar funcional em qualquer máquina nova

* [ ] Python 3.12 instalado
* [ ] Node 20 LTS instalado
* [ ] Docker + Docker Compose
* [ ] Postgres 16 (via Docker)
* [ ] Redis 7 (via Docker)
* [ ] pre-commit hooks (lint + format)
* [ ] script `make setup` que sobe tudo
* [ ] script `make seed` que popula dados de teste
* [ ] script `make reset` que limpa tudo
* [ ] arquivo `.env.example` documentado
* [ ] README de "primeiro dia" para novo dev

Documentação em `infrastructure/CLAUDE.md` e `backend/CLAUDE.md`.

---

## 5.4 Repositórios e CI/CD shells

* [ ] repositório monorepo criado no GitHub
* [ ] estrutura de pastas seguindo `docs/project-map.md`
* [ ] `.gitignore` apropriado (Python + Node + IDE)
* [ ] proteções de branch (`main` requer PR + CI verde)
* [ ] GitHub Actions workflow stub (lint vazio, espera código)
* [ ] Renovate ou Dependabot ativado
* [ ] templates de PR e Issue
* [ ] CODEOWNERS apontando para founder

---

## 5.5 Decisões contratuais e legais

* [ ] CNPJ da empresa Clinix constituído
* [ ] contrato modelo SaaS com clínica revisado por advogado
* [ ] termo de uso do produto redigido
* [ ] política de privacidade redigida (LGPD)
* [ ] DPA (Data Processing Agreement) para clínicas (a Clinix é operador)
* [ ] termo de consentimento padrão do paciente
* [ ] termo específico para fotos clínicas (estética/dermato)
* [ ] termo opt-in para marketing
* [ ] verificação de conformidade com CFM/CFO/CRP/CRN/CRBM/CREFITO

Documentação consolidada em `docs/lgpd.md` e `docs/compliance-conselhos.md`.

---

## 5.6 Estratégia comercial inicial

* [ ] pricing definido (Starter / Professional / Business)
* [ ] proposta comercial para clínica piloto
* [ ] discurso de venda alinhado (apresentação)
* [ ] vídeo curto demonstrando design (não código)
* [ ] landing page mínima (`clinix.app`) com formulário de interesse
* [ ] análise da clínica piloto-alvo (qual das listadas em `POSSÍVEIS CLIENTES.MD`)

Detalhes em `docs/roadmap/10-go-to-market.md`.

---

## 5.7 Templates clínicos seed

* [ ] templates para cada uma das 8 especialidades pré-rascunhados em JSON Schema
  * [ ] medicina geral
  * [ ] odontologia
  * [ ] fisioterapia
  * [ ] psicologia
  * [ ] biomedicina
  * [ ] dermatologia
  * [ ] estética
  * [ ] nutrição
* [ ] validação com profissional de cada área (entrevista de 30 min)
* [ ] correções aplicadas

Detalhes em `docs/templates-clinicos.md`.

---

## 5.8 Catálogos seed

* [ ] base de medicamentos comuns (com dosagem padrão)
* [ ] base de procedimentos odontológicos (TUSS quando aplicável)
* [ ] base de procedimentos estéticos
* [ ] base TACO de alimentos (CSV importável)
* [ ] catálogo de escalas validadas (PHQ-9, GAD-7, DASS-21, BDI-II)
* [ ] catálogo de contraindicações estéticas
* [ ] CID-10 (subset relevante)

---

# 6. ENTREGÁVEIS FINAIS DA PRÉ-FASE 0

Ao final desta fase, o time tem:

* design system completo com biblioteca Figma
* todas as contas externas provisionadas e segredos no cofre
* ambiente local rodando em qualquer máquina em < 30 min
* repositório criado, CI configurado (esperando código)
* contratos legais aprovados
* templates clínicos validados
* clínica piloto identificada e contatada
* pricing definido

---

# 7. CRITÉRIO DE PRONTO

Não começar Sprint 01 antes de:

* [ ] aprovação visual do design system pelo founder
* [ ] todas as contas externas em modo produção (não trial)
* [ ] `make setup` funcionando em máquina limpa
* [ ] CI rodando hello-world com sucesso
* [ ] termos legais revisados por advogado
* [ ] pelo menos 1 clínica piloto identificada e propensa

---

# 8. RISCOS

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Design system atrasa | média | alto | priorizar telas críticas; resto vira polish |
| Conta WhatsApp bloqueada | baixa | crítico | comprar chip dedicado; usar só para clínica; templates aprovados |
| Validação clínica não acontece | média | médio | entrevistar via WhatsApp/áudio; aceitar imperfeição inicial |
| LGPD revisão demora | média | médio | template padrão da OAB + ajuste mínimo |
| Founder fica preso na operação | alta | alto | timeboxing rigoroso (3 sem max); seguir mesmo se 80% pronto |

---

# 9. ANTI-METAS

NÃO fazer na Pré-Fase 0:

* escrever código de produção (apenas scripts auxiliares)
* configurar produção real (apenas staging mockado)
* polir design além do MVP (perfeccionismo paralisa)
* construir landing page rica (mínima é suficiente)
* vender para 10 clínicas (1–3 é o foco)

---

# 10. CUSTOS ESTIMADOS

| Item | Custo |
|------|-------|
| Domínio | R$ 50/ano |
| VPS Hostinger | R$ 60–150/mês |
| VPS Evolution | R$ 60/mês |
| Cloudflare R2 | gratuito (volume baixo) |
| Sentry | gratuito |
| Resend | gratuito |
| 1Password Business | R$ 50/mês |
| Designer freelance (40h) | R$ 4.000–8.000 |
| Advogado (revisão LGPD) | R$ 1.500–3.000 |
| WhatsApp Business chip | R$ 50/mês |
| **Total inicial** | **~R$ 8.000–13.000** |

---

# 11. KPIs DE SAÍDA

* [ ] 100% das telas críticas com mockup aprovado
* [ ] 8/8 templates clínicos validados
* [ ] `make setup` < 30 minutos em máquina limpa
* [ ] CI verde com hello-world
* [ ] 1+ clínica piloto identificada

---

# 12. HANDOFF PARA FASE 0

Quando esta fase termina:

* Sprint 01 pode começar **imediatamente** sem bloqueios externos
* `tasks/sprint-01.md` é o próximo documento a executar
* design system serve como referência durante toda a Fase 0

---

# 13. REFERÊNCIAS

* `docs/vision.md`
* `docs/design-system.md`
* `docs/stack.md`
* `docs/personalizacao.md`
* `docs/lgpd.md`
* `docs/compliance-conselhos.md`
* `docs/templates-clinicos.md`
* `POSSÍVEIS CLIENTES.MD`
* `tasks/sprint-01.md` (próximo)
* `docs/roadmap/10-go-to-market.md` (paralelo)
