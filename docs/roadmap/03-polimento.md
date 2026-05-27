# Fase 1.5 — Polimento

> Estabilização pós-piloto. Transforma o MVP em produto comercializável repetidamente.

---

# 1. OBJETIVO

Após 30 dias do piloto em produção, esta fase:

* corrige bugs descobertos em operação real
* aplica feedback estruturado em refinamentos
* otimiza performance dos pontos críticos
* aprova templates WhatsApp na Meta
* enriquece portal do paciente
* coleta primeiro NPS estruturado
* prepara onboarding repetível para próximas clínicas

Ao final, o produto está **maduro para vender para a segunda, terceira e quinta clínica** sem retrabalho operacional.

---

# 2. PRÉ-REQUISITOS

Fase 1 concluída:

* clínica piloto em produção há 30+ dias
* feedback estruturado coletado
* runbook mínimo escrito
* alarmes críticos configurados

---

# 3. DURAÇÃO ESTIMADA

**6 semanas** (Sprint 05: 3 sem + Sprint 06: 3 sem).

Pode estender 2–4 semanas se feedback do piloto for substancial.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Backend dev | 1 | 1 |
| Frontend dev | 1 | 1 |
| QA | — | 1 |
| CS dedicado | — | 1 (acompanhar piloto + onboarding novas clínicas) |
| Advogado/DPO | consulta | retainer |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 05 — Bugs do Piloto + Performance + UX (3 sem)

Arquivo dedicado: `tasks/sprint-05.md`.

### Foco

* corrigir bugs reportados pela clínica piloto
* otimizar top 10 queries mais lentas
* refinar empty states, error states, skeletons
* atalhos de teclado e busca global
* hardening operacional (runbook + alertas)

### Entregáveis principais

* [ ] zero bugs P0 abertos
* [ ] top 10 queries < 200ms p95
* [ ] primeira tela < 1.5s p95
* [ ] runbook de incidentes documentado
* [ ] dashboard de saúde do sistema

---

## 5.2 Sprint 06 — LGPD + Meta-approval + Portal v2 (3 sem)

Arquivo dedicado: `tasks/sprint-06.md`.

### Foco

* LGPD hardening (DPIA simplificada, exercer direitos, anonimização)
* 15+ templates WhatsApp aprovados na Meta
* portal do paciente v2 (OTP, pré-anamnese, gráficos, PWA)
* primeiro NPS estruturado
* onboarding repetível (vídeos, KB inicial, proposta padronizada)

### Entregáveis principais

* [ ] termos LGPD revisados por advogado
* [ ] 15+ templates Meta aprovados
* [ ] portal v2 usado por > 70% dos pacientes ativos
* [ ] NPS médio > 8
* [ ] 5+ artigos de KB publicados
* [ ] primeira venda pós-MVP fechada

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| Bugs P0/P1 resolvidos | 05 | crítico | P0 |
| Performance top 10 queries | 05 | médio | P0 |
| Refinos UX | 05 | médio | P0 |
| Runbook de incidentes | 05 | médio | P0 |
| Alertas críticos Slack/Discord | 05 | médio | P0 |
| Auditoria LGPD interna | 06 | alto | P0 |
| Painel de direitos do paciente | 06 | alto | P0 |
| Anonimização Celery scheduled | 06 | médio | P0 |
| Termos LGPD revisados | 06 | médio | P0 |
| Templates Meta aprovados | 06 | crítico | P0 |
| OTP via WhatsApp | 06 | médio | P0 |
| Portal PWA | 06 | médio | P0 |
| NPS automatizado | 06 | baixo | P0 |
| KB e vídeos | 06 | baixo | P1 |
| Roteiro de demo | 06 | baixo | P1 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| pg_stat_statements | top queries em prod |
| django-debug-toolbar (staging) | N+1 detection |
| @next/bundle-analyzer | otimização bundle |
| FCM (preparação) | push para Fase 4 |
| jspdf / docx-to-pdf (revisão) | exports Excel/iCal |
| Service Worker | PWA |

---

# 8. KPIs DE SAÍDA

* [ ] zero bugs P0 abertos
* [ ] < 5 bugs P1 abertos
* [ ] NPS médio > 8 (paciente)
* [ ] NPS interno > 8 (clínica piloto)
* [ ] 15+ templates Meta aprovados
* [ ] portal v2 usado por > 70% dos pacientes
* [ ] PWA instalável em Android e iOS
* [ ] clínica piloto declara redução de trabalho manual mensurável
* [ ] 2–3 clínicas adicionais em onboarding ativo
* [ ] primeira venda pós-MVP fechada (paga, não simbólica)

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Feedback do piloto inflando escopo | alta | alto | timeboxing; backlog rigoroso |
| Meta demora aprovação | alta | médio | submeter todos no início; fallback genérico |
| DPO requisito surpreende | média | médio | founder atua como DPO inicial |
| PWA com bugs em iOS | alta | médio | testar em device real; aceitar limitações |
| Refator implícito como "polish" | alta | médio | code review rigoroso |
| Clínica piloto desiste | baixa | crítico | comunicação constante + ajuste rápido |

---

# 10. ANTI-METAS

NÃO fazer na Fase 1.5:

* CRM (Sprint 07 — Fase 2)
* multi-unidade (Sprint 07)
* convênios (Sprint 08)
* IA (Fase 3)
* mobile (Fase 4)
* otimização prematura (apenas top 10 medido)
* refactor grande sem ADR
* customização por clínica individual (esforço x retorno ruim)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| VPS Hostinger (prod + staging) | R$ 150/mês × 1.5 meses = R$ 225 |
| VPS Evolution | R$ 60/mês × 1.5 = R$ 90 |
| Cloudflare R2 | R$ 50–100/mês |
| Sentry | upgrade para Team se necessário ~$26/mês |
| Designer (refinamento) | R$ 2.000 |
| Advogado (DPO / LGPD) | R$ 2.000–4.000 |
| CS hora-extra | R$ 2.000 |
| **Total adicional** | **~R$ 6.500–8.500** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 1.5 termina quando:

* [ ] todos os KPIs da seção 8 estão verdes
* [ ] clínica piloto continua satisfeita (entrevista mensal positiva)
* [ ] 2+ clínicas pagantes em onboarding ativo (não só piloto simbólico)
* [ ] processo de venda documentado e replicável
* [ ] processo de onboarding documentado e replicável
* [ ] base de conhecimento online (>10 artigos)

Quando esses pontos verdes, **Sprint 07 (Fase 2) pode começar**.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — primeiras vendas pagas; ajuste do pricing; piloto vira case
* **Cadência operacional** — primeiro postmortem se houver incidente; SLA documentado por plano; primeiro retro mensal de produto

---

# 14. REFERÊNCIAS

* `tasks/sprint-05.md`
* `tasks/sprint-06.md`
* `docs/lgpd.md`
* `docs/whatsapp.md`
* `docs/portal-paciente.md`
* `docs/performance.md`
* `docs/suporte.md`
* `docs/roadmap/04-expansao.md` (próxima fase)
* `docs/roadmap/10-go-to-market.md` (paralelo)
* `docs/roadmap/11-cadencia-operacional.md` (paralelo)
