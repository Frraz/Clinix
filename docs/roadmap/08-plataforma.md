# Fase 5 — Plataforma

> Transformar o Clinix em ecossistema extensível: API pública, SDK, webhooks, marketplace de integrações.

---

# 1. OBJETIVO

Até aqui, o Clinix é um **produto**. Esta fase torna-o uma **plataforma**:

* **API pública** documentada (OpenAPI) com autenticação por token
* **Webhooks completos** (cliente recebe eventos)
* **SDK em JS/TS** (consumo simplificado por integradores)
* **Marketplace de integrações** (catálogo + on/off por tenant)
* **Integrações nativas** com ERPs, contabilidade, gateways, equipamentos médicos
* **Open Data** seletivo para BI externo (com consentimento da clínica)
* **DevRel** mínimo (documentação, exemplos, comunidade)

Receita potencial: revenue share com integradores parceiros + planos enterprise.

---

# 2. PRÉ-REQUISITOS

* Fase 4 (Mobile) concluída ou paralela
* 30+ clínicas pagantes
* MRR > R$ 30.000/mês
* API interna estável (já é "API-first" desde Fase 0; agora vira pública)
* time de DevRel ou DevOps com bandwidth para suporte

---

# 3. DURAÇÃO ESTIMADA

**Contínuo** (Sprints 24–28, ~3 semanas cada, mas com cadência aberta).

Plataforma evolui sob demanda; novas integrações entram conforme parceiros.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Backend dev (API + SDK) | 1 | 2 |
| DevRel (documentação + suporte) | 1 parcial | 1 dedicado |
| Frontend (marketplace UI) | 1 | 1 |
| Designer | parcial | 1 |
| BD / parcerias | 1 | 1 |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 24 — API Pública v1 + Documentação (3 sem)

* OpenAPI spec gerada automaticamente
* documentação pública (Swagger UI / Redoc) em `developers.clinix.app`
* gerenciamento de API tokens por tenant (admin gera, revoga)
* rate limiting tier-based
* versionamento explícito (`/api/public/v1/`)
* sandbox para testes
* exemplos em cURL, Python, JavaScript

## 5.2 Sprint 25 — Webhooks Completos (3 sem)

* catálogo de eventos públicos (`patient.created`, `appointment.completed`, etc.)
* endpoint de configuração de webhook por tenant
* assinatura HMAC para validação
* retry com backoff exponencial
* dead-letter queue
* dashboard de webhooks (entregues / falhas / latência)

## 5.3 Sprint 26 — SDK JS/TS + Marketplace (3 sem)

* SDK em TypeScript publicado em npm (`@clinix/sdk`)
* tipos compartilhados com a API pública
* exemplos prontos (Node, Next.js, Cloudflare Workers)
* marketplace de integrações no painel admin
* on/off de integração por tenant
* gerenciamento de credenciais (criptografadas)

## 5.4 Sprint 27 — Integrações Nativas (3 sem)

* contabilidade (Conta Azul, Bling, Omie)
* ERPs locais (parcerias regionais)
* gateways adicionais (PagSeguro, Asaas)
* Google Calendar (sync agenda)
* Doctoralia (sync agenda — bidirecional)
* Boa Consulta / Conexa Saúde (telemedicina)
* equipamentos médicos via HL7 (preparação)

## 5.5 Sprint 28 — DICOM + Equipamentos + Open Data (3 sem)

* DICOM viewer (imagens radiológicas)
* integração com PACS (visualização)
* drivers HL7 para equipamentos comuns
* import automático de resultado de equipamento
* Open Data: API somente-leitura para BI externo (com consentimento da clínica)
* webhooks especializados para integrações de imagem

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| OpenAPI auto-gerado | 24 | médio | P0 |
| Documentação pública (Redoc) | 24 | médio | P0 |
| API tokens + rate limit | 24 | médio | P0 |
| Sandbox | 24 | médio | P0 |
| Webhooks catalog | 25 | médio | P0 |
| Webhook config UI | 25 | médio | P0 |
| HMAC + retry + DLQ | 25 | alto | P0 |
| Dashboard webhooks | 25 | médio | P1 |
| SDK JS/TS publicado | 26 | médio | P0 |
| Marketplace UI | 26 | médio | P0 |
| Integração contabilidade | 27 | médio | P1 |
| Integração Google Calendar | 27 | médio | P1 |
| Integração Doctoralia | 27 | alto | P1 |
| Drivers HL7 (5 equipamentos) | 28 | alto | P2 |
| DICOM viewer | 28 | alto | P2 |
| Open Data API | 28 | médio | P2 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| drf-spectacular | OpenAPI auto-gerado |
| Redoc | documentação pública |
| ts-rest ou direct types | SDK type-safe |
| HL7apy | HL7 |
| dicom-parser / dicomjs | DICOM client |
| Orthanc (servidor PACS) | armazenamento DICOM (futuro) |

---

# 8. KPIs DE SAÍDA

* [ ] 5+ integrações publicadas no marketplace
* [ ] 10+ tenants usando API pública
* [ ] 100+ webhooks configurados (todos os tenants)
* [ ] SDK com 100+ downloads/mês
* [ ] uptime API pública 99.9%
* [ ] documentação com NPS > 8 (dev experience)
* [ ] revenue share com 1+ parceiro

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| API pública vira vetor de ataque | média | crítico | rate limit agressivo + observability + WAF |
| Webhooks sobrecarregam clínicas | alta | médio | rate limit + DLQ; cliente pode pausar |
| SDK quebra com breaking change | alta | alto | semver rigoroso; deprecation 6+ meses |
| Doctoralia / Google API mudam | alta | alto | adapter isolado; alerta de quebra |
| HL7/DICOM complexos | alta | médio | priorizar 5 equipamentos comuns; resto sob demanda |
| Marketplace sem parceiros | alta | médio | começar com integrações próprias; recrutar parceiros |

---

# 10. ANTI-METAS

NÃO fazer na Fase 5:

* abrir API antes da estabilidade interna
* permitir webhooks sem rate limit
* SDK em N linguagens (foco JS/TS primeiro; Python depois sob demanda)
* integrar com TODOS os ERPs (apenas demandados)
* PACS próprio (apenas viewer + integração)
* revenue share complexo (simples primeiro)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| Infra (mais carga API) | R$ 500–1.000/mês adicional |
| DevRel dedicado | R$ 8.000–15.000/mês |
| BD / parcerias | R$ 5.000–10.000/mês |
| Equipamentos para teste HL7 | R$ 0 (parceiros emprestam) |
| Documentação dev (writer) | R$ 5.000 uma vez |
| **Total adicional (5 sprints)** | **~R$ 80.000–130.000** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 5 é **contínua**. Marcos:

* [ ] API pública v1 estável (12+ meses sem breaking)
* [ ] SDK publicado e mantido
* [ ] marketplace com 5+ integrações
* [ ] 10%+ do MRR vem de planos com integrações ativas

Próxima fase (Enterprise) começa quando primeiros contratos enterprise aparecem.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — programa de parceiros; marketing dev-focused; presença em eventos de tecnologia em saúde (HIMSS, HealthTech Brasil)
* **Cadência operacional** — SLA para API pública; postmortems público quando incidente; status page público

---

# 14. REFERÊNCIAS

* `docs/api.md` — especificação técnica
* `docs/workflows.md` — eventos canônicos
* `ADRs/001-api-first.md`
* `ADRs/006-event-driven.md`
* `docs/roadmap/07-mobile.md` (fase anterior)
* `docs/roadmap/09-enterprise.md` (próxima fase)
