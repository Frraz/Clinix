# Fase 2 — Expansão

> Aprofundar features que destravam clínicas maiores e fechar 10–20 clínicas pagantes.

---

# 1. OBJETIVO

Após o piloto estabilizado, esta fase:

* atende clínicas com **convênios** (não só particular)
* atende clínicas com **filiais** (multi-unidade real)
* introduz **CRM operacional** completo
* automações **low-code** (editor visual)
* **assinatura digital ICP-Brasil** (documentos com valor legal)
* **estoque** controlado
* **migração avançada** de outros sistemas (parsers + API)
* prepara para **escala 10–50 clínicas**

Ao final, o Clinix é **competitivo com iClinic / Ninsaúde** em escopo regional.

---

# 2. PRÉ-REQUISITOS

Fase 1.5 concluída:

* piloto estável
* 2+ clínicas pagantes em onboarding
* NPS > 8
* templates Meta aprovados
* base de conhecimento publicada

---

# 3. DURAÇÃO ESTIMADA

**4–6 meses** (Sprints 07–12, ~4 semanas cada).

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Backend dev | 1 | 2 |
| Frontend dev | 1 | 1 |
| Designer | 1 dedicado | 1 |
| QA | parcial | 1 |
| Product manager | parcial | 1 |
| CS | 1 | 2 |
| DevOps | parcial | parcial |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 07 — CRM + Multi-unidade (4 sem)

Arquivo dedicado: `tasks/sprint-07.md`.

* CRM pipeline + funil
* multi-unidade com branding por unidade
* automações de relacionamento (faltosos, inadimplentes)
* migração de tenants para multi-unidade

---

## 5.2 Sprint 08 — Convênios + TISS + Estoque (4 sem)

Arquivo dedicado: `tasks/sprint-08.md`.

* convênios (catálogo, autorização, faturamento)
* TISS básico (PDFs + XMLs)
* estoque (medicamentos, produtos estéticos, materiais)
* DRE mensal automatizado

---

## 5.3 Sprint 09 — Workflow Builder + Assinatura ICP-Brasil (4 sem)

* editor visual de workflows (drag-and-drop)
* biblioteca de templates de workflow
* assinatura digital ICP-Brasil em receitas, atestados, laudos
* validação de certificado A1/A3
* armazenamento seguro de assinaturas

---

## 5.4 Sprint 10 — Migração Avançada + Import via API (4 sem)

* parsers dedicados para iClinic
* parsers dedicados para Doctoralia
* parsers dedicados para Ninsaúde
* import via REST API (mapeamento + reconciliação)
* sync incremental opcional (legados que persistem em paralelo)
* dashboard de progresso de migração

---

## 5.5 Sprint 11 — Domínio Próprio + Personalização Avançada (4 sem)

* domínio próprio do portal do paciente (`portal.suaclinica.com.br`)
* CNAME + SSL automático via Cloudflare for SaaS
* white-label parcial (tela de login com branding)
* templates de documento totalmente customizáveis (camada 2)
* preferências de notificação por paciente

---

## 5.6 Sprint 12 — BI Inicial + Convênios Avançado (4 sem)

* dashboards configuráveis por gestor
* BI básico (relatórios mensais por especialidade)
* integração direta com APIs de convênios (onde disponível)
* webhooks de saída para o cliente (cliente pode receber eventos)
* SMS via Twilio/Zenvia como fallback para WhatsApp crítico

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| CRM pipeline (kanban) | 07 | médio | P0 |
| Funil faltosos/inadimplentes | 07 | médio | P0 |
| Multi-unidade completa | 07 | alto | P0 |
| Branding por unidade | 07 | médio | P0 |
| Convênios CRUD | 08 | alto | P0 |
| TISS 4.0 (PDFs + XML) | 08 | crítico | P0 |
| Estoque com validade | 08 | médio | P0 |
| DRE mensal | 08 | médio | P1 |
| Workflow builder visual | 09 | alto | P0 |
| Templates de workflow | 09 | médio | P0 |
| Assinatura ICP-Brasil | 09 | crítico | P0 |
| Migração iClinic | 10 | alto | P0 |
| Migração Doctoralia | 10 | alto | P1 |
| Migração Ninsaúde | 10 | médio | P1 |
| Import via API REST | 10 | médio | P1 |
| Sync incremental | 10 | alto | P2 |
| Domínio próprio portal | 11 | médio | P0 |
| White-label parcial | 11 | médio | P1 |
| Templates documento customizáveis | 11 | médio | P1 |
| BI inicial | 12 | médio | P0 |
| Convênios API direta | 12 | alto | P1 |
| Webhooks de saída | 12 | médio | P1 |
| SMS fallback | 12 | médio | P2 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| Cloudflare for SaaS | domínio próprio com SSL |
| python-pkcs11 / pyHanko | assinatura ICP-Brasil |
| lxml + xmlschema | TISS XML validation |
| react-flow | editor visual de workflow |
| Metabase ou Apache Superset | BI inicial (alternativa) |
| Twilio / Zenvia | SMS fallback |
| Apache POI (xlsx) | exports avançados |

---

# 8. KPIs DE SAÍDA

* [ ] 10+ clínicas pagantes ativas
* [ ] churn mensal < 5%
* [ ] MRR sustentando time mínimo (~ R$ 5.000/mês líquido)
* [ ] 3+ clínicas com convênio ativo
* [ ] 2+ clínicas multi-unidade
* [ ] 5+ migrações de outros sistemas concluídas
* [ ] NPS médio > 8
* [ ] 50+ pacientes ativos por clínica em média
* [ ] uptime 99.5% (mensal)

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| TISS 4.0 muda | média | alto | acompanhar publicações ANS; estrutura preparada para upgrade |
| Convênios cobram integração paga | alta | médio | priorizar TISS PDF/XML; integração API só onde ROI claro |
| Migração de iClinic complexa | alta | alto | parser robusto; CS acompanha 1ª migração; aceitar perdas marginais |
| Workflow builder pesado | média | médio | UI simples primeiro; complexidade incremental |
| ICP-Brasil tem custo (cert A3) | média | médio | suportar A1 (software); A3 (token) como opcional |
| Multi-unidade quebra tenants existentes | média | crítico | migração reversível + dry-run em staging |
| Time não escala (1–2 devs gargalo) | alta | alto | terceirizar specific work (designer, devops) |
| Concorrência aprende e copia | média | médio | velocidade > defesa; nicho regional |

---

# 10. ANTI-METAS

NÃO fazer na Fase 2:

* operação laboratorial completa (Fase 2.5)
* IA clínica (Fase 3)
* mobile (Fase 4)
* SSO enterprise (Fase 6)
* customização visual avançada além do branding (Fase 6)
* atendimento a redes/franquias (Fase 6)
* integração com TODOS os convênios (apenas 3–5 maiores)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| VPS upgrade (mais clínicas) | R$ 300/mês × 5 meses = R$ 1.500 |
| Cloudflare R2 + for SaaS | R$ 200/mês = R$ 1.000 |
| Sentry Team | $26/mês × 5 = $130 |
| Resend Pro | $20/mês × 5 = $100 |
| Evolution VPS escalada | R$ 150/mês × 5 = R$ 750 |
| Mercado Pago/Stripe (comissões) | depende do volume |
| Twilio/Zenvia SMS | R$ 100/mês = R$ 500 |
| Certificado ICP-Brasil | R$ 200 (uma vez) |
| Designer (telas novas) | R$ 5.000 |
| Advogado (revisões) | R$ 2.000 |
| 2º dev contratado (parcial) | R$ 15.000–25.000 |
| Designer dedicado | R$ 8.000–15.000 |
| **Total adicional (5 meses)** | **~R$ 35.000–55.000** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 2 termina quando:

* [ ] 10+ clínicas pagantes ativas
* [ ] churn < 5%/mês
* [ ] MRR > custo operacional + 1 dev
* [ ] convênios em produção (3+ clínicas usando)
* [ ] multi-unidade em produção (2+ clínicas)
* [ ] workflow builder em produção (5+ workflows customizados)
* [ ] migração validada de pelo menos 2 sistemas concorrentes
* [ ] NPS estável > 8

Decisão estratégica entre **Fase 2.5 (Laboratório)** ou **Fase 3 (IA)** depende de:

* % de clínicas com lab interno (justifica Fase 2.5)
* avanço de concorrentes em IA (acelera Fase 3 se concorrência aperta)

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — venda ativa para clínicas regionais; primeiros estudos de caso públicos; participação em eventos médicos locais
* **Cadência operacional** — on-call rotation iniciada; SLA por plano; segurança trimestral; LGPD audit anual

---

# 14. REFERÊNCIAS

* `tasks/sprint-07.md` a `tasks/sprint-08.md` (detalhados)
* sprints 09–12 (a criar em `tasks/` quando entrarem no horizonte de 2 sprints)
* `docs/convenios.md`
* `docs/estoque.md`
* `docs/crm.md`
* `docs/workflows.md`
* `docs/importacao-exportacao.md`
* `docs/roadmap/05-laboratorio.md` (próxima fase 2.5)
* `docs/roadmap/06-inteligencia.md` (próxima fase 3)
* `docs/roadmap/10-go-to-market.md` (paralelo)
* `docs/roadmap/11-cadencia-operacional.md` (paralelo)
