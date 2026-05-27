# Sprint 08 — Convênios + TISS Básico + Estoque

> Segunda sprint da Fase 2. Foco em destravar clínicas que dependem de convênios e que controlam estoque.

---

# 1. OBJETIVO

* operação de convênios (cadastro, autorização, faturamento básico)
* TISS básico (envio de guias eletrônicas para os 3–5 maiores convênios)
* estoque (medicamentos, produtos estéticos, materiais consumíveis)
* relatórios financeiros enriquecidos

---

# 2. DURAÇÃO ESTIMADA

4 semanas.

---

# 3. PRÉ-REQUISITOS

Sprint 07 concluída:

* CRM operacional
* multi-unidade em produção
* automações refinadas

---

# 4. ESCOPO

## Convênios

* [ ] modelo `Insurance` (catálogo de convênios da clínica)
* [ ] modelo `InsurancePlan` (planos dentro de cada convênio)
* [ ] modelo `PatientInsurance` (paciente + convênio + carteirinha)
* [ ] tabelas de preço por convênio (procedimentos cobertos)
* [ ] modelo `Authorization` (autorização prévia quando exigida)
* [ ] modelo `InsuranceClaim` (faturamento)
* [ ] modelo `InsuranceGlossa` (glosa)
* [ ] vínculo `Appointment` ↔ `Insurance`
* [ ] tela de gestão de convênios
* [ ] tela de cobertura por procedimento
* [ ] integração com prontuário (campo CID obrigatório para convênio)

Detalhamento em `docs/convenios.md`.

---

## TISS Básico

* [ ] geração de guia SP/SADT em PDF
* [ ] geração de guia de consulta em PDF
* [ ] geração de guia de honorário individual em PDF
* [ ] XML TISS 4.0 (formato simplificado)
* [ ] vínculo com `InsuranceClaim`
* [ ] dashboard de faturamento de convênio (recebido / a receber / glosado)
* [ ] estágio 1: apenas geração de PDFs e XMLs (envio manual)
* [ ] estágio 2 (Sprint 09–12): integração direta com convênios via API quando disponível

---

## Estoque

* [ ] modelo `Product` (com `product_type`: medicamento, produto estético, material, consumível)
* [ ] modelo `ProductBatch` (lote + validade)
* [ ] modelo `StockMovement` (entrada/saída/ajuste)
* [ ] alerta de validade próxima (D-30, D-15, vencido)
* [ ] alerta de estoque baixo (configurável por produto)
* [ ] inventário (contagem física + ajuste)
* [ ] vínculo com procedimentos (consumo automático ao executar sessão)
* [ ] modelo `Supplier` (fornecedor)
* [ ] modelo `PurchaseOrder` (pedido de compra)
* [ ] relatório de consumo por período
* [ ] integração com sessões de pacote (consumo automático)

Detalhamento em `docs/estoque.md`.

---

## Relatórios financeiros enriquecidos

* [ ] DRE mensal automatizado
* [ ] fluxo de caixa projetado (30/60/90 dias)
* [ ] inadimplência por período
* [ ] receita por convênio
* [ ] receita por procedimento
* [ ] comissões por profissional (cálculo automático configurável)
* [ ] repasse a profissionais
* [ ] export para contabilidade (formato compatível)

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* 3+ clínicas com convênio ativo
* TISS gerado para Unimed, Bradesco Saúde, Amil (3 maiores)
* clínica estética com estoque controlado de produtos
* DRE mensal automático gerado
* 1–2 clínicas pediram migração para Clinix por causa do TISS

---

# 6. NÃO ESTÁ NO ESCOPO

* integração direta com APIs de convênios (Sprint 09–12)
* assinatura ICP-Brasil (Sprint 09–12)
* operação laboratorial completa (Fase 2.5)
* IA (Fase 3)

---

# 7. RISCOS

* TISS 4.0 muda padrão
  * mitigação: implementar versão atual; estrutura preparada para upgrade
* convênios cobram integrações pagas
  * mitigação: estágio 1 com geração manual; integração paga só onde retorno justifica
* estoque vira blocker (cálculo errado)
  * mitigação: implementação simples primeiro; bloqueio de venda só em produtos críticos
* glosas frequentes
  * mitigação: validação prévia de cobertura no ato do atendimento

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando
* code review aprovado
* validação manual com clínica que usa convênio
* XML TISS validado contra schema oficial

---

# 9. PRÓXIMA SPRINT

Sprints 09–12 cobrirão (em ordem flexível conforme demanda):

* automações low-code (editor visual de workflows)
* assinatura digital ICP-Brasil
* import via API REST (migração de outros sistemas)
* domínio próprio do portal do paciente
* convênios — integração direta com APIs disponíveis
* relatórios avançados e BI inicial

Após Sprint 12, **Fase 2 conclui** e Fase 2.5 (Laboratório completo) ou Fase 3 (IA) começa baseado em demanda do mercado.
