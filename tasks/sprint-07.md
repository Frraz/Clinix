# Sprint 07 — CRM + Multi-unidade

> Primeira sprint da Fase 2. Foco em destravar clínicas com filiais e clínicas que querem gerenciar relacionamento ativamente.

---

# 1. OBJETIVO

* CRM com pipeline de leads + funil de captação
* multi-unidade (filiais com branding e profissionais por unidade)
* automações de relacionamento (recuperação de faltoso avançada, reativação)
* preparação de infraestrutura para 10+ clínicas

---

# 2. DURAÇÃO ESTIMADA

4 semanas.

---

# 3. PRÉ-REQUISITOS

Fase 1.5 concluída:

* piloto estável
* templates Meta aprovados
* portal v2 em uso
* NPS > 8

---

# 4. ESCOPO

## CRM — Pipeline de Leads

* [ ] modelo `Lead` (sem virar paciente ainda)
* [ ] modelo `LeadStage` (custom por clínica)
* [ ] estágios seed: novo, qualificado, agendado avaliação, fez avaliação, ganho, perdido
* [ ] modelo `LeadInteraction` (mensagem, ligação, visita)
* [ ] kanban view de pipeline no frontend
* [ ] drag-and-drop entre estágios
* [ ] origem do lead (WhatsApp, Instagram, indicação, site)
* [ ] automação: lead novo → mensagem boas-vindas
* [ ] automação: lead frio (sem interação > 7 dias) → reativação
* [ ] conversão lead → paciente preserva histórico
* [ ] dashboard de funil (taxa de conversão por estágio)

---

## CRM — Funil de Faltosos e Inadimplentes

* [ ] funil de recuperação de faltosos
* [ ] funil de inadimplência (cobrança preventiva D-3, D-1, D+1, D+7)
* [ ] segmentação básica de pacientes (tags)
* [ ] campanhas WhatsApp para segmento
* [ ] respeitar opt-out

---

## Multi-unidade

* [ ] modelo `Unit` vinculado a `Tenant`
* [ ] propagação de `unit_id` em entidades relevantes (`Appointment`, `User`, etc.)
* [ ] branding por unidade (logo, cor, endereço, telefone)
* [ ] horários e profissionais por unidade
* [ ] agenda filtrável por unidade
* [ ] relatórios financeiros por unidade
* [ ] permissões: gerente de unidade vê só sua unidade
* [ ] PDFs com branding da unidade emissora
* [ ] migração: tenants existentes ganham unidade padrão automática

---

## Automações operacionais avançadas

* [ ] motor de workflow refinado (mais tipos de gatilhos)
* [ ] automação: aniversário do paciente → mensagem
* [ ] automação: paciente inativo > 90 dias → reativação
* [ ] automação: pacote terminando → upsell
* [ ] painel de automações ligadas/desligadas por tenant
* [ ] log de execução de cada automação

---

## Performance e escala

* [ ] revisão de queries com volume crescente
* [ ] particionamento opcional de tabelas grandes (audit log)
* [ ] limite de armazenamento por tenant aplicado (plano)
* [ ] alertas de uso (80% do plano)

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* CRM operacional em 3+ clínicas (pipeline ativo)
* clínica multi-unidade convertida e operando
* 5+ automações ativas além das do MVP
* taxa de conversão de lead medida pela primeira vez

---

# 6. NÃO ESTÁ NO ESCOPO

* convênios/TISS (Sprint 08)
* assinatura ICP-Brasil (Sprint 09–12)
* editor visual de workflow (Sprint 09–12)
* IA (Fase 3)
* mobile (Fase 4)

---

# 7. RISCOS

* migração de tenants existentes para multi-unidade quebra
  * mitigação: migração reversível + dry-run em staging com dados reais
* CRM vira "outro CRM"
  * mitigação: focar em integração com agenda/financeiro; não competir com Pipedrive
* automações disparam em loop
  * mitigação: idempotência + rate limit por tenant

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando
* code review aprovado
* validação em staging com dados copiados de prod (anonimizados)
* migração testada em ambiente isolado

---

# 9. PRÓXIMA SPRINT

Sprint 08 cobrirá:

* convênios (operação básica)
* TISS básico (envio de guias eletrônicas)
* estoque (medicamentos, produtos estéticos, materiais)

Ver `tasks/sprint-08.md`.
