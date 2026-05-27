# Fase 2.5 — Laboratório Completo

> Operação laboratorial interna completa. Atende laboratórios independentes e clínicas com lab interno.

---

# 1. OBJETIVO

No MVP (Fase 1), o módulo de laboratório atende apenas **solicitação + upload de laudo externo**. Esta fase implementa a operação completa:

* coleta de amostras
* triagem com barcode
* processamento por área (hematologia, bioquímica, microbiologia)
* validação técnica e clínica
* assinatura digital de laudo
* entrega ao paciente (portal + WhatsApp)
* SLA por exame
* integração com convênios para faturamento
* integração futura com equipamentos (HL7/ASTM)

Ao final, o Clinix atende **laboratórios independentes** como mercado próprio.

---

# 2. PRÉ-REQUISITOS

Fase 2 concluída:

* convênios em produção
* assinatura ICP-Brasil operacional
* 10+ clínicas pagantes
* validação de demanda de mercado laboratorial (entrevistas com 3+ labs)

---

# 3. DURAÇÃO ESTIMADA

**2–3 meses** (Sprints 13–15, ~4 semanas cada).

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Backend dev | 1 | 2 |
| Frontend dev | 1 | 1 |
| Consultor lab | 1 (parcial) | 1 dedicado |
| QA | 1 | 1 |
| CS especializado em lab | parcial | 1 |

Consultor laboratorial é crucial: alguém com vivência prática de biomedicina/análises clínicas.

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 13 — Pedido + Coleta + Triagem (4 sem)

* modelo `LaboratoryOrder`, `LaboratorySample`, `LaboratoryExam`
* solicitação de exames (via prontuário ou avulsa)
* coleta agendada
* coleta domiciliar (com endereço + roteiro do coletor)
* impressão de etiquetas com barcode
* leitura de barcode na triagem
* validação de integridade da amostra
* fila por área de processamento

## 5.2 Sprint 14 — Processamento + Validação + Liberação (4 sem)

* modelo `LaboratoryResult`
* entrada manual de resultado (sem equipamento ainda)
* validação técnica (biomédico)
* validação clínica (responsável técnico)
* repetição de exame com justificativa
* liberação de laudo
* assinatura ICP-Brasil aplicada
* PDF de laudo final com layout padrão
* SLA por exame (alerta de atraso)

## 5.3 Sprint 15 — Entrega + Dashboard + Convênios (4 sem)

* entrega via portal do paciente (com URL assinada)
* notificação WhatsApp e email
* download seguro com TTL curto
* dashboard de SLA (taxa de cumprimento, gargalos)
* dashboard de produtividade por técnico
* integração com convênios para faturamento (TISS Lab)
* retificação de laudo com versionamento
* lab parceiro (terceirização de análises)

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| Modelos lab (Order/Sample/Exam) | 13 | crítico | P0 |
| Solicitação de exame via prontuário | 13 | médio | P0 |
| Solicitação avulsa | 13 | médio | P0 |
| Coleta agendada | 13 | médio | P0 |
| Coleta domiciliar | 13 | médio | P1 |
| Etiquetas com barcode | 13 | médio | P0 |
| Triagem com leitura | 13 | médio | P0 |
| Fila por área | 13 | médio | P0 |
| Entrada manual de resultado | 14 | médio | P0 |
| Validação técnica | 14 | alto | P0 |
| Validação clínica | 14 | alto | P0 |
| Liberação com assinatura | 14 | crítico | P0 |
| PDF de laudo padrão | 14 | médio | P0 |
| SLA com alerta | 14 | médio | P0 |
| Entrega via portal | 15 | médio | P0 |
| Notificação WhatsApp | 15 | médio | P0 |
| Dashboard SLA | 15 | médio | P0 |
| Faturamento TISS Lab | 15 | alto | P1 |
| Retificação versionada | 15 | médio | P1 |
| Lab parceiro (terceirização) | 15 | médio | P2 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| python-barcode + Pillow | geração de etiquetas |
| ZXing (camera barcode) | leitura barcode no frontend |
| pyHanko / pyKCS11 | assinatura ICP-Brasil (já em Fase 2) |
| HL7apy / hl7 (Python) | preparação para integração futura |
| Bluetooth printer SDK | impressão térmica (futuro) |

---

# 8. KPIs DE SAÍDA

* [ ] 2+ laboratórios em operação completa
* [ ] 1.000+ exames processados/mês
* [ ] SLA cumprido em > 90% dos exames
* [ ] zero perda de amostra (auditoria)
* [ ] 100% dos laudos assinados digitalmente
* [ ] taxa de retificação < 2%
* [ ] NPS de lab > 8

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Workflow laboratorial complexo demais | alta | alto | consultor lab obrigatório; iteração com lab parceiro |
| Equipamentos não integram | alta | médio | entrada manual primeiro; integração HL7 só após validar demanda |
| Faturamento TISS Lab tem regras próprias | média | alto | implementar TISS Lab depois do TISS clínico |
| Perda de amostra (rastreabilidade) | baixa | crítico | barcode obrigatório + audit log + alertas de gap |
| Laudo errado liberado | baixa | crítico | dupla validação + versionamento + notificação automática |
| Concorrência laboratorial forte | alta | médio | nicho: integração com clínica multi-especialidade |
| ICP-Brasil falha intermitentemente | média | alto | retry + fallback para assinatura interna registrada em audit |

---

# 10. ANTI-METAS

NÃO fazer na Fase 2.5:

* anatomia patológica complexa (mercado próprio, fora de escopo)
* radiologia/imagem (DICOM, Fase 5 plataforma)
* lab clínico hospitalar (volume e regulação diferentes)
* genética molecular (fora de escopo)
* integração com TODOS os equipamentos (apenas demanda real)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| VPS upgrade (volume lab) | R$ 400/mês × 3 meses = R$ 1.200 |
| Consultor lab | R$ 5.000–10.000 |
| Impressoras térmicas (kit lab piloto) | R$ 1.500 (lab compra) |
| Leitores barcode | R$ 300 cada (lab compra) |
| Designer (telas lab) | R$ 4.000 |
| Advogado (compliance lab — RDC ANVISA) | R$ 3.000 |
| **Total adicional** | **~R$ 13.200–18.200** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 2.5 termina quando:

* [ ] 2+ laboratórios em produção há 30+ dias
* [ ] SLA > 90% em ambos
* [ ] zero incidente crítico
* [ ] auditoria de rastreabilidade passa
* [ ] faturamento TISS Lab funcionando (ao menos 1 convênio)

A partir daqui, decisão entre **Fase 3 (IA)** ou **Fase 4 (Mobile)** baseada em demanda do mercado.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — abertura de novo segmento (laboratórios); comissões diferenciadas; eventos do setor lab
* **Cadência operacional** — RDC ANVISA review; auditoria de rastreabilidade; runbook específico de lab

---

# 14. REFERÊNCIAS

* `docs/laboratorio.md` — especificação técnica
* `examples/fluxo-laboratorio.md` — fluxo completo
* `docs/convenios.md`
* `docs/compliance-conselhos.md`
* `docs/auditoria.md`
* `docs/roadmap/04-expansao.md` (fase anterior)
* `docs/roadmap/06-inteligencia.md` (próxima ou Fase 4)
