# Fase 1 — MVP

> Produto vendável para a primeira clínica piloto em qualquer das 8 especialidades-alvo.

---

# 1. OBJETIVO

Entregar um sistema **operável fim-a-fim** em produção real, atendendo:

* clínica piloto (real, paga ou simbólica)
* até 10 profissionais
* 1 unidade
* qualquer uma das 8 especialidades-alvo (ou múltiplas combinadas)

Não é beta. Não é demo. É produto real operado por humanos reais.

---

# 2. PRÉ-REQUISITOS

Fase 0 concluída (ver `docs/roadmap/01-fundacao.md`):

* multi-tenancy operacional
* auth + MFA + RBAC
* CRUD de pacientes + agenda base
* design system implementado
* staging operacional
* CI/CD funcional

---

# 3. DURAÇÃO ESTIMADA

**6–8 semanas** (Sprint 03: 3–4 sem + Sprint 04: 3–4 sem).

Realista para 1 dev fullstack focado. Com 2 devs, possível em 5–6 semanas.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável | Acelerado |
|-------|--------|-------------|-----------|
| Backend dev | 1 | 1 | 2 |
| Frontend dev | 1 (mesmo dev) | 1 | 1 |
| Designer | ad-hoc | 1 | 1 |
| QA | — | parcial | 1 |
| CS (onboarding piloto) | — | parcial | 1 |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 03 — Prontuário Composable + Financeiro Essencial (3–4 sem)

Arquivo dedicado: `tasks/sprint-03.md`.

### Foco

* engine de prontuário composable (servindo 8 especialidades)
* templates seed por especialidade
* prescrições/atestados/recibos em PDF (WeasyPrint)
* financeiro essencial (PIX, cartão via Mercado Pago)
* dashboards operacionais básicos

### Entregáveis principais

* [ ] 8 templates seed validados em produção
* [ ] schema dinâmico validado backend e frontend (Zod)
* [ ] PDFs com branding do tenant
* [ ] PIX e cartão recebendo (sandbox e produção)
* [ ] webhook Mercado Pago idempotente
* [ ] caixa do dia renderizado
* [ ] anotações `is_private` para psicologia funcionando

---

## 5.2 Sprint 04 — WhatsApp + Portal + Componentes Específicos (3–4 sem)

Arquivo dedicado: `tasks/sprint-04.md`.

### Foco

* WhatsApp transacional (Evolution API)
* automações operacionais (confirmação, lembrete, pós-consulta)
* portal do paciente
* componentes específicos: odontograma, mapa de lesões, antropometria, pacotes, fotos antes/depois, escalas validadas

### Entregáveis principais

* [ ] 500+ mensagens WhatsApp enviadas
* [ ] portal acessado por 50+ pacientes
* [ ] 50+ pacotes ativos (estética/odonto/fisio)
* [ ] 100+ fotos clínicas com consentimento
* [ ] 30+ planos alimentares enviados
* [ ] odontograma e mapa de lesões em produção
* [ ] escalas PHQ-9/GAD-7/DASS-21 com cálculo automático

---

# 6. MATRIZ DE COBERTURA POR ESPECIALIDADE

| Especialidade | Atendido por | Componente específico | Sprint |
|---------------|--------------|-----------------------|--------|
| Medicina geral | prontuário composable + SOAP | — | 03 |
| Odontologia | prontuário + odontograma + pacotes | `DentalChart`, `TreatmentPlan` | 03 + 04 |
| Fisioterapia | prontuário + pacotes + avaliação funcional | `TherapyPlan` | 03 + 04 |
| Psicologia | prontuário + escalas + sigilo | `ScaleApplication`, `is_private` | 03 + 04 |
| Biomedicina | solicitação + upload de laudo | (Fase 2.5 para op completa) | 03 + 04 |
| Dermatologia | prontuário + mapa lesões + dermatoscopia | `SkinLesionMap`, `Lesion` | 04 |
| Estética | prontuário + pacotes + contraindicações + fotos | `EstheticAssessment`, `BeforeAfterPhoto` | 04 |
| Nutrição | prontuário + antropometria + plano alimentar | `NutritionPlan`, `AnthropometryRecord` | 04 |

Após a Fase 1, **todas as 8 áreas estão atendidas em nível funcional mínimo**.

---

# 7. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| Engine de templates composable | 03 | crítico | P0 |
| 8 templates seed | 03 | alto | P0 |
| Validação dinâmica BE+FE | 03 | crítico | P0 |
| PDFs (receita, atestado, recibo) | 03 | médio | P0 |
| WeasyPrint + branding | 03 | médio | P0 |
| Mercado Pago PIX + cartão | 03 | alto | P0 |
| Webhook idempotente | 03 | crítico | P0 |
| Dashboards (meu dia + caixa) | 03 | médio | P0 |
| Evolution API integrada | 04 | crítico | P0 |
| Templates WhatsApp (10+ aprovados) | 04 | alto | P0 |
| Automação confirmação D-1 | 04 | médio | P0 |
| Automação lembrete 2h | 04 | médio | P0 |
| Automação pós-consulta | 04 | baixo | P0 |
| Portal do paciente | 04 | alto | P0 |
| OTP via WhatsApp | 04 | médio | P0 |
| Check-in digital | 04 | médio | P0 |
| Odontograma (UI + dados) | 04 | alto | P0 |
| Mapa corporal de lesões | 04 | alto | P0 |
| Pacotes de tratamento | 04 | alto | P0 |
| Foto antes/depois com slider | 04 | médio | P0 |
| Antropometria + bioimpedância | 04 | médio | P0 |
| Editor de plano alimentar | 04 | alto | P0 |
| Escalas validadas | 04 | médio | P0 |
| Upload de imagens com Celery | 04 | médio | P0 |

---

# 8. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que | Doc |
|------|----------|-----|
| WeasyPrint | geração de PDFs | `docs/stack.md` |
| Evolution API (VPS) | WhatsApp | `ADRs/008-whatsapp-evolution-api.md` |
| Mercado Pago SDK | pagamentos | `ADRs/009-pagamentos-mercadopago-stripe.md` |
| TipTap | rich-text no prontuário | `docs/stack.md` |
| FullCalendar | agenda (já em Sprint 02) | — |
| Recharts | gráficos | `docs/stack.md` |
| react-hook-form + Zod | formulários | `docs/stack.md` |
| pillow + pillow-heif + pillow-avif | compressão imagem | `docs/imagens-clinicas.md` |
| Celery Beat | jobs periódicos | `docs/stack.md` |

---

# 9. KPIs DE SAÍDA

* [ ] clínica piloto **em produção real** por **30 dias sem incidente crítico**
* [ ] NPS interno > 8
* [ ] feedback estruturado coletado (entrevistas + planilha)
* [ ] p95 < 500ms em endpoints críticos
* [ ] primeira tela < 2s
* [ ] cobertura de testes ≥ 70% em services
* [ ] testes E2E para fluxos: login, agendar, atender em cada das 8 especialidades, cobrar, enviar WhatsApp
* [ ] zero vazamento de tenant em testes
* [ ] zero PII no Sentry (scrubbing validado)

---

# 10. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Engine de templates atrasa | média | crítico | priorizar 2–3 especialidades primeiro, demais com template genérico |
| Evolution API instabiliza | alta | alto | monitoramento Sentry + fallback para SMS/email |
| Meta bloqueia número WhatsApp | média | crítico | apenas templates aprovados; opt-out rigoroso; chip dedicado |
| Mercado Pago webhook duplica | alta | médio | idempotência por event_id obrigatória |
| PDFs gerados ficam lentos | média | médio | geração em Celery + cache de URL |
| Fotos clínicas degradam performance | média | alto | presigned PUT + Celery + thumbs + lazy load |
| Sigilo psicológico vazado | baixa | crítico | RBAC + audit log em leitura + tests |
| Founder gargalo (1 dev) | alta | alto | timeboxing rigoroso; cortar componentes específicos para 6 áreas mínimas |
| Clínica piloto desiste | média | alto | manter 2 leads alternativos engajados |
| Customizações por clínica piloto | alta | médio | dizer "não" — wait Fase 1.5 |

---

# 11. ANTI-METAS

NÃO fazer no MVP:

* TISS e convênios complexos (Fase 2)
* CRM com pipeline e funis (Fase 2)
* automações low-code (editor visual) — Fase 2
* IA clínica — Fase 3
* mobile — Fase 4
* telemedicina — Fase 4
* multi-unidade (Fase 2)
* operação laboratorial interna completa — Fase 2.5
* assinatura digital ICP-Brasil — Fase 2 (MVP usa assinatura simples)
* BI avançado — Fase 3
* marketplace de integrações — Fase 5
* SSO enterprise — Fase 6
* customização visual além de logo + cor

---

# 12. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| VPS Hostinger (prod + staging) | R$ 150/mês × 2 meses = R$ 300 |
| VPS Evolution API | R$ 60/mês × 2 meses = R$ 120 |
| Cloudflare R2 (volume crescente) | R$ 0–50/mês |
| Sentry | R$ 0 (free tier) |
| Resend | R$ 0 (free tier) |
| Mercado Pago | comissão por transação |
| Mockup de produção em staging | R$ 0 |
| Designer (refinamentos) | R$ 2.000 |
| Advogado (revisão termos finais) | R$ 1.000 |
| **Total adicional** | **~R$ 3.500** |

---

# 13. CRITÉRIO DE PRONTO / HANDOFF

A Fase 1 só termina quando:

* [ ] clínica piloto está em **produção real**
* [ ] 30 dias completos sem incidente crítico
* [ ] NPS interno > 8
* [ ] feedback estruturado coletado em entrevistas
* [ ] roadmap da Fase 1.5 ajustado com base no feedback real
* [ ] runbook de produção mínimo escrito
* [ ] alarmes críticos configurados (Sentry + healthcheck)

Quando esses pontos verdes, **Sprint 05 (Fase 1.5) pode começar**.

---

# 14. PARALELISMO COM TRACKS

* **Go-to-market** — durante a Fase 1, conversas com clínicas alternativas continuam (lead pipeline para Fase 2). Tutorial e vídeos demo gravados após primeira clínica em produção.
* **Cadência operacional** — pair programming/code review reforçado durante construção do prontuário composable; primeiro postmortem (se houver incidente) pratica o ritual.

---

# 15. REFERÊNCIAS

* `tasks/mvp.md` — definição completa de escopo
* `tasks/sprint-03.md` — execução detalhada Sprint 03
* `tasks/sprint-04.md` — execução detalhada Sprint 04
* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/dermatologia.md` / `estetica.md` / `nutricao.md`
* `docs/odontograma.md` / `escalas-testes.md` / `antropometria.md`
* `docs/imagens-clinicas.md` / `pacotes-tratamento.md`
* `docs/whatsapp.md` / `docs/portal-paciente.md`
* `docs/billing.md`
* `examples/fluxo-prontuario.md`
* `examples/fluxo-financeiro.md`
* `examples/fluxo-whatsapp.md`
* `examples/fluxo-portal-paciente.md`
* `examples/fluxo-pacote-tratamento.md`
* `examples/fluxo-plano-alimentar.md`
* `docs/roadmap/03-polimento.md` (próxima fase)
