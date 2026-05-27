# Fase 3 — Inteligência (IA)

> IA clínica, operacional e conversacional como diferencial real, não marketing.

---

# 1. OBJETIVO

Introduzir IA útil de verdade em múltiplas camadas:

* **IA clínica**: sumarização de prontuário, sugestão de CID, SOAP assistido
* **IA operacional**: previsão de no-show, previsão de inadimplência, análise de gargalos
* **IA conversacional**: assistente interno (recepção, gestor), chatbot do portal
* **IA por especialidade**: casos de uso específicos (análise dermatoscópica, aderência nutricional, sinalização de escalas psicológicas)
* **alertas inteligentes** baseados em padrões

Sempre como **assistente**, nunca substituto do profissional. Sempre com **opt-in explícito** da clínica.

---

# 2. PRÉ-REQUISITOS

Fase 2 concluída (e ideialmente Fase 2.5 também):

* 10+ clínicas pagantes
* dados suficientes em produção (5.000+ consultas, 50.000+ entradas de prontuário)
* templates clínicos estáveis
* aceitação cultural ("IA pode ajudar?")
* runway financeiro para custo de API LLM

---

# 3. DURAÇÃO ESTIMADA

**3–4 meses** (Sprints 16–19, ~3 semanas cada).

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| ML/AI engineer | 1 (parcial) | 1 dedicado |
| Backend dev | 2 | 2 |
| Frontend dev | 1 | 1 |
| Designer | parcial | 1 |
| Data engineer | — | 1 |
| QA com viés ML | parcial | 1 |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 16 — Infraestrutura de IA + Sumarização Clínica (3 sem)

* arquitetura de prompt management (versionamento)
* PII scrubbing antes de qualquer chamada LLM
* DPA com OpenAI / Anthropic
* cache de respostas (Redis) para reuso ético
* feedback loop (aceito / rejeitado / editado)
* sumarização de prontuário (1ª feature)
* opt-in por clínica + opt-out por paciente
* tela de feedback no profissional

## 5.2 Sprint 17 — Sugestão CID + SOAP Assistido (3 sem)

* sugestão de CID-10 baseada em descrição clínica
* sumarização da consulta em SOAP estruturado
* preenchimento assistido de evolução
* sempre revisável antes de salvar
* templates de prompt por especialidade

## 5.3 Sprint 18 — IA Operacional (no-show + inadimplência + gargalos) (3 sem)

* modelo de previsão de no-show (scikit-learn ou XGBoost)
* features: histórico do paciente, tipo de consulta, dia da semana, distância, profissional, clima
* sugestão de overbooking baseada no risco
* modelo de previsão de inadimplência
* alertas operacionais (gargalo de agenda, queda de produtividade)
* dashboard de IA operacional

## 5.4 Sprint 19 — Assistente Conversacional + IA por Especialidade (3 sem)

* chat interno: "quais pacientes faltaram 2x esse mês?", "quanto faturei hoje?"
* NLP → query estruturada → resposta
* chatbot do portal do paciente (dúvidas básicas, escopo limitado)
* análise ABCDE assistida em imagem dermatoscópica
* sinalização de escalas psicológicas em risco
* sugestão de substituições nutricionais (compatíveis com restrições)
* analytics preditivo (LTV, churn por clínica)

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| Prompt management + versionamento | 16 | médio | P0 |
| PII scrubbing pre-LLM | 16 | crítico | P0 |
| DPA com OpenAI/Anthropic | 16 | crítico | P0 |
| Cache Redis para respostas | 16 | médio | P0 |
| Feedback loop | 16 | médio | P0 |
| Sumarização clínica | 16 | médio | P0 |
| Sugestão CID | 17 | médio | P0 |
| SOAP assistido | 17 | alto | P0 |
| Templates de prompt por especialidade | 17 | médio | P0 |
| Modelo no-show (scikit-learn) | 18 | alto | P0 |
| Sugestão overbooking | 18 | médio | P1 |
| Modelo inadimplência | 18 | médio | P1 |
| Alertas operacionais | 18 | médio | P1 |
| Dashboard IA operacional | 18 | médio | P1 |
| Chat interno (NLP → query) | 19 | alto | P0 |
| Chatbot portal | 19 | médio | P1 |
| ABCDE dermatoscopia | 19 | alto | P1 |
| Sinalização escalas | 19 | médio | P0 |
| Sugestões nutricionais | 19 | médio | P1 |
| Analytics preditivo | 19 | médio | P2 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| OpenAI API (gpt-4o, gpt-5) | LLM |
| Anthropic Claude API | LLM alternativo |
| LangChain ou direct SDK | orquestração |
| scikit-learn | modelos de previsão |
| XGBoost (opcional) | classificação |
| pandas + polars | feature engineering |
| MLflow ou wandb | experiment tracking |
| pgvector (Postgres extension) | busca semântica futura |
| Cloud Run / Lambda (opcional) | inferência separada se escalar |

---

# 8. KPIs DE SAÍDA

* [ ] aceitação > 60% em sugestões de CID
* [ ] aceitação > 70% em sumarizações
* [ ] previsão de no-show com precisão > 75%
* [ ] redução de no-show medida > 15% nas clínicas que ativaram
* [ ] uso ativo de chat interno por 50%+ dos admins
* [ ] tempo médio de prontuário reduzido > 20% nas clínicas com IA
* [ ] feedback positivo dos profissionais (entrevistas + NPS específico de IA)
* [ ] custo de IA < 5% do MRR

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| LLM alucina conteúdo clínico | alta | crítico | sempre mostrar como sugestão; revisão obrigatória; templates restritos |
| Vazamento de PII para provider | média | crítico | PII scrubbing obrigatório + DPA + audit log |
| Custo de API explode | média | alto | cache agressivo + tier de plano + cap por clínica |
| Profissionais rejeitam IA | média | alto | onboarding educativo; opt-in; mostrar valor antes |
| Bias em modelo de previsão | média | médio | validação por especialidade; sem decisões automáticas críticas |
| Regulação CFM/CFO sobre IA muda | média | médio | monitorar publicações; sempre "assistente, não autônoma" |
| Concorrência avança em IA | alta | médio | velocidade > perfeição; nichar nas 8 especialidades |

---

# 10. ANTI-METAS

NÃO fazer na Fase 3:

* IA generativa de diagnóstico autônomo (regulação delicada)
* IA generativa de imagens médicas (regulação delicada)
* substituir profissional em decisão clínica
* treinar modelos próprios com dados sem consentimento explícito
* feature de IA sem opt-in
* IA conversacional sem moderação (alucinação livre)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| OpenAI / Anthropic API | $200–2000/mês inicial → escala |
| Compute para inferência ML | R$ 200/mês |
| MLflow / tooling | R$ 0–500/mês |
| ML engineer | R$ 12.000–20.000/mês × 4 meses |
| Designer (telas IA) | R$ 5.000 |
| Consultor médico (validação) | R$ 5.000 |
| **Total adicional (4 meses)** | **~R$ 55.000–95.000** |

Esta é a fase mais cara até aqui — exige MRR maduro para sustentar.

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 3 termina quando:

* [ ] aceitação medida > 60% em sugestões clínicas
* [ ] redução de no-show documentada > 15%
* [ ] tempo médio de prontuário reduzido medido
* [ ] 50%+ das clínicas ativaram pelo menos 1 feature de IA
* [ ] custo de IA < 5% do MRR

Próximo: **Fase 4 (Mobile)** se demanda apertar, ou continuar refinando IA.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — IA como diferencial em vendas; estudos de caso "Clinix reduziu X% de no-show"; selo "Clinic AI-powered"
* **Cadência operacional** — review ético de cada feature IA; monitoring específico de aceitação; postmortems de sugestões erradas

---

# 14. REFERÊNCIAS

* `docs/ia.md` — especificação técnica
* `docs/compliance-conselhos.md` — regulação CFM/CFO sobre IA
* `docs/lgpd.md` — tratamento de dados
* `docs/auditoria.md` — audit log de IA
* `docs/roadmap/04-expansao.md` (fase anterior)
* `docs/roadmap/07-mobile.md` (próxima fase)
