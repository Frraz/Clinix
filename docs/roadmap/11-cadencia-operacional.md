# Track Paralelo — Cadência Operacional

> Rituais, processos e práticas que mantêm o Clinix saudável em todas as fases. Nunca pausa.

---

# 1. VISÃO GERAL

Construir software é apenas metade do trabalho. Manter saudável exige:

* **rituais de engenharia** (planning, review, retro)
* **práticas de qualidade** (code review, testes, lint)
* **segurança contínua** (audits, dependency updates, LGPD review)
* **operação 24/7** progressiva (on-call rotation)
* **postmortems** sem culpa
* **runbooks** atualizados
* **disaster recovery** testado
* **ferramental** adequado por tamanho de time

Este track começa **na Pré-Fase 0** e nunca pausa.

---

# 2. RITUAIS POR CADÊNCIA

## 2.1 Diário (15 min)

* **standup assíncrono** no canal #standup do Slack/Discord
  * o que fiz ontem
  * o que farei hoje
  * bloqueios
* **revisão de Sentry** (10 min) — alertas críticos da última 24h
* **revisão de PRs abertos** — não deixar PR > 24h aberto

---

## 2.2 Semanal

* **planning da sprint** (30 min, segunda) — revisão do que está pendente, ajuste de prioridades
* **review da sprint** (30 min, sexta) — o que foi entregue, o que ficou
* **retro curta** (15 min, sexta) — o que funcionou, o que ajustar
* **revisão de uptime** (10 min) — status page, alertas, capacity
* **revisão comercial** (15 min) — leads, conversões (com track de go-to-market)

---

## 2.3 Quinzenal / Sprint

* **sprint completa** (30 min) — fechamento + planning da próxima
* **revisão de Sentry estendida** (30 min) — todos os erros, não só críticos
* **revisão de queries lentas** (30 min) — `pg_stat_statements` + ações

---

## 2.4 Mensal

* **all-hands** (1h) — status da empresa, roadmap, métricas
* **retro de produto** (1h) — feedback dos clientes, ajustes de roadmap
* **revisão de NPS** (30 min) — análise + ações
* **revisão de churn** (30 min) — quem saiu, por quê
* **revisão de custo de infra** (15 min)
* **dependency updates** (Renovate ou Dependabot) — aprovar e mergear
* **postmortems do mês** (se houver incidente)

---

## 2.5 Trimestral

* **revisão de roadmap** (2h) — ajustar com base no realizado
* **security review** (4h) — revisão de permissões, secrets, configs
* **DR test** (2h) — restaurar backup em ambiente isolado, validar RTO/RPO
* **revisão de SLOs** (1h) — ajustar se necessário
* **revisão de templates clínicos** (1h) — feedback dos profissionais
* **revisão de compliance LGPD** (1h)

---

## 2.6 Anual

* **LGPD audit completa** (advogado externo, 1–2 dias)
* **security audit completo** (externo, opcional)
* **review de stack** (devemos trocar algo?)
* **planejamento estratégico** (3 dias, off-site)
* **revisão de equipe** (1×1 estendido)

---

# 3. CODE REVIEW

## 3.1 Regras

* **todo PR exige aprovação** (1 reviewer mínimo, 2 para mudanças críticas)
* **CI verde obrigatório** antes do merge
* **branch protection** em `main` (PR + checks)
* **CODEOWNERS** apontando responsáveis por path
* **PR pequeno preferível** — < 400 linhas de diff
* **commit messages claros** (conventional commits opcional)

## 3.2 Checklist de revisão

* [ ] testes incluídos (ou justificativa)
* [ ] documentação atualizada (se aplicável)
* [ ] tenant isolation verificada
* [ ] RBAC validado
* [ ] sem PII em logs
* [ ] sem secrets hardcoded
* [ ] performance considerada (N+1, queries, índices)
* [ ] frontend acessível (axe-core)
* [ ] mobile testado (se aplicável)

---

# 4. TESTES OBRIGATÓRIOS

## 4.1 Backend

* `pytest -m tenancy` passa sempre (zero vazamento entre tenants)
* unit tests para services
* integration tests para endpoints críticos
* migration tests (forward + backward em staging)
* cobertura mínima: 70% em services e regras de negócio

## 4.2 Frontend

* Vitest para hooks e utilitários
* Testing Library para componentes interativos
* Playwright para fluxos E2E críticos
* axe-core no CI (acessibilidade)

## 4.3 Mobile (Fase 4+)

* Jest para lógica
* Detox para E2E
* manual testing em devices reais

---

# 5. SEGURANÇA CONTÍNUA

## 5.1 Práticas obrigatórias

* **MFA obrigatório** para admin/profissional/financeiro
* **secrets em cofre** (1Password) — nunca commitados
* **rotação de chaves** (Stripe, Mercado Pago, etc.) trimestral
* **revisão de IAM** trimestral (acessos de devs)
* **bug bounty** informal — convidar amigos a quebrar staging

## 5.2 Pentests

* anual (externo) — Fase 3+
* semestral (interno com ferramentas) — Fase 2+

## 5.3 Monitoring

* Sentry com alertas Slack/Discord
* UptimeRobot para healthchecks externos
* Cloudflare WAF com rules customizadas
* alertas de:
  * brute-force tentativas
  * spike de erros
  * queries demoradas
  * uso anormal de R2
  * tentativas de acesso fora de horário

---

# 6. DEPENDENCY MANAGEMENT

## 6.1 Ferramentas

* **Renovate** ou **Dependabot** — PRs automáticos
* **Snyk** ou **GitHub Security Alerts** — CVEs

## 6.2 Ritual

* **semanal** — revisar PRs do Renovate
* **mensal** — mergear minor/patch updates aprovados pelo CI
* **trimestral** — major updates planejados (com migration guide)

## 6.3 Política

* nunca usar dependência sem mantedor ativo (último commit > 1 ano = warning)
* nunca usar dependência com CVE crítico aberto
* preferir dependências de fundações conhecidas (Django Software Foundation, Vercel, etc.)

---

# 7. ON-CALL

## 7.1 Quando iniciar

* **Fase 1.5+** — quando primeira clínica em produção
* primeiro: founder é on-call 24/7 (inevitável)
* **Fase 2** — rotação de 2+ pessoas
* **Fase 3+** — rotação semanal com hand-off documentado

## 7.2 Ferramental

* **Slack/Discord** com webhooks Sentry
* **PagerDuty** (Fase 3+) para SMS/voz em incidente crítico
* **runbooks** atualizados em `docs/runbooks/`

## 7.3 SLA interno

| Severidade | Resposta | Resolução |
|------------|----------|-----------|
| SEV1 (sistema fora do ar) | 15 min | 1h |
| SEV2 (degradação crítica) | 30 min | 4h |
| SEV3 (degradação parcial) | 2h | 24h |
| SEV4 (bug não-crítico) | 24h | sprint atual |

---

# 8. POSTMORTEMS

## 8.1 Quando

* todo incidente SEV1 ou SEV2
* problemas de segurança
* perda de dados (mesmo parcial)
* breach de SLA

## 8.2 Estrutura (blameless)

* **summary** — o que aconteceu
* **timeline** — minuto a minuto
* **impacto** — clínicas afetadas, dados afetados, tempo
* **causa raiz** — análise técnica
* **o que funcionou** — detecção, resposta
* **o que não funcionou** — gaps, lentidão
* **ações** — concretas, com owner e prazo
* **lições** — para a base de conhecimento

## 8.3 Onde

* arquivos em pasta `docs/postmortems/` (criar na Fase 1.5+), nomeados `YYYY-MM-DD-titulo.md`
* compartilhado internamente
* parcialmente compartilhado com cliente afetado (transparência)

---

# 9. RUNBOOKS

> Pasta `docs/runbooks/` será criada na Fase 1.5+ (quando produção real demanda). Arquivos abaixo são o plano mínimo de runbooks a manter.

Documentos operacionais planejados para situações comuns:

* deploy normal e rollback
* incidente: vazamento de tenant
* incidente: Evolution API down
* incidente: webhook Mercado Pago falhando
* incidente: spike de erros no Sentry
* restore de backup
* onboarding de novo tenant
* offboarding de tenant
* migração de outro sistema
* DR test

Atualizados sempre após postmortem.

---

# 10. DISASTER RECOVERY

## 10.1 RTO e RPO

| Plano | RTO | RPO |
|-------|-----|-----|
| Starter | 8h | 24h |
| Professional | 4h | 12h |
| Business | 2h | 4h |
| Enterprise | 1h | 15 min (PITR) |

## 10.2 Backups

* **Postgres** — `pg_dump` diário + WAL contínuo (Fase 2+)
* **R2** — versionamento de bucket + lifecycle rules
* **redundância geográfica** — backup secundário em outro provider (Backblaze B2)

## 10.3 Testes

* **trimestral** — restaurar backup completo em ambiente isolado
* **semestral** — simulação de DR full (failover)

Detalhamento em `docs/backup-dr.md`.

---

# 11. FERRAMENTAL POR FASE

## Fase 0–1

* GitHub (código + CI)
* Slack ou Discord (comunicação)
* 1Password (secrets)
* Notion ou Linear (tasks; opcional)
* Sentry (erros)
* UptimeRobot (uptime)

## Fase 2+

* Linear (tasks estruturadas)
* Loom (vídeos curtos de demo/handoff)
* Figma (design system + iteração)
* Metabase (BI interno)
* Status page (statuspage.io ou Atlassian)

## Fase 3+

* PagerDuty
* Datadog (opcional, custo justifica)
* MLflow (experiment tracking)

## Fase 4+

* App Store Connect + Play Console
* Sentry React Native

## Fase 5+

* Readme.io ou Mintlify (docs públicas)
* DevRel community (Discord/Slack público)

---

# 12. ROLES E RESPONSABILIDADES

## Fase 0–1 (founder solo)

Founder acumula:

* Tech Lead
* Backend Dev
* Frontend Dev
* DevOps
* Designer (com freelancer)
* CS
* Sales
* PM

## Fase 1.5+ (primeira contratação)

Adiciona:

* dev fullstack OU
* CS / customer success

## Fase 2+

* PM ou Product Designer
* mais um dev
* QA parcial

## Fase 3+

* ML/AI engineer
* dedicated CS

## Fase 4+

* Mobile dev
* dedicated designer

## Fase 5+

* DevRel
* BD / parcerias

## Fase 6

* SRE
* Account manager enterprise
* Security engineer

---

# 13. GOVERNANÇA DE CÓDIGO

## 13.1 ADRs

* toda decisão arquitetural significativa → ADR
* numerados sequencialmente
* sem retroatividade (ADR vale a partir da data)
* revisão por code review

## 13.2 Mudanças de produto

* toda feature P0 → PRD curto em `docs/specs/` (1 página)
* mudanças de modelo do banco → migration test em staging com dados reais (anonimizados)
* mudanças de API pública → deprecation 6 meses

## 13.3 Quality gates

* sem PR sem testes
* sem release sem CI verde
* sem deploy em produção sem aprovação
* sem hotfix sem postmortem na semana seguinte

---

# 14. PROCESSO DE INCIDENTE

```txt
Detecção (Sentry, UptimeRobot, cliente)
   ↓
Alerta (Slack/Discord)
   ↓
On-call assume (15 min máx)
   ↓
Status inicial em status page
   ↓
Investigação + mitigação
   ↓
Comunicação com clientes afetados
   ↓
Resolução
   ↓
Status final em status page
   ↓
Postmortem (até 5 dias)
   ↓
Ações implementadas
   ↓
Base de conhecimento atualizada
```

---

# 15. LGPD — RITUAL CONTÍNUO

* **trimestral** — revisão de tratamento de dados
* **trimestral** — verificação de PII scrubbing no Sentry
* **anual** — DPIA atualizada
* **anual** — auditoria externa
* **sob demanda** — atender pedidos do art. 18 (acesso, exclusão, portabilidade) em < 15 dias

Detalhamento em `docs/lgpd.md`.

---

# 16. CULTURA

## Princípios

* **simplicidade > complexidade**
* **clareza > esperteza**
* **ship > perfeccionismo**
* **lições > culpa** (blameless)
* **cliente é parceiro**, não inimigo
* **dados decidem**, não opinião

## Não-negociáveis

* nunca culpar pessoa por incidente
* nunca esconder erro
* nunca pular code review (mesmo founder)
* nunca commitar segredo
* nunca expor PII em log
* nunca degradar para "depois a gente arruma"

---

# 17. REFERÊNCIAS

* `docs/auditoria.md`
* `docs/backup-dr.md`
* `docs/lgpd.md`
* `docs/performance.md`
* `docs/suporte.md`
* `docs/roadmap.md`
* `tasks/bugs.md`
* `ADRs/` (todas)
* `docs/roadmap/10-go-to-market.md` (paralelo)
