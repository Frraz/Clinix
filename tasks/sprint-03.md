# Sprint 03 — Prontuário composable + Financeiro essencial

> Terceira sprint do Clinix. Entrega do prontuário modular que serve todas as 8 especialidades + módulo financeiro pronto para operar.

---

# 1. OBJETIVO

Entregar a **engine de prontuário composable** que serve todas as especialidades do MVP via templates dinâmicos, mais o módulo financeiro essencial:

* prontuário com builder de templates por especialidade
* templates seed para as 8 especialidades-alvo
* prescrições, atestados e laudos em PDF
* financeiro operacional (cobrança, PIX, cartão, recibo)
* dashboards básicos de caixa e produção

Ao fim desta sprint, o profissional já consegue atender um paciente fim-a-fim em qualquer especialidade e cobrar.

---

# 2. DURAÇÃO ESTIMADA

3–4 semanas.

---

# 3. PRÉ-REQUISITOS

Sprint 02 concluída:

* RBAC granular
* CRUD de pacientes
* agenda base com check-in
* staging operando

---

# 4. ESCOPO

## Prontuário composable (engine)

* [ ] modelo `Specialty` (medicina, odonto, fisio, psico, biomed, derm, estética, nutri)
* [ ] modelo `RecordTemplate` (template versionado por especialidade, com `schema_json`)
* [ ] modelo `MedicalRecord` (vinculado a paciente, profissional, especialidade)
* [ ] modelo `RecordEntry` (entradas individuais, com `content_json` baseado em template)
* [ ] modelo `RecordVersion` (versionamento por entrada)
* [ ] modelo `RecordAttachment` (anexos no R2)
* [ ] serializer dinâmico que valida `content_json` contra `schema_json` do template
* [ ] endpoint `GET /api/v1/records/templates/?specialty=X`
* [ ] endpoint `POST /api/v1/records/{patient_id}/entries/`
* [ ] endpoint `GET /api/v1/records/{patient_id}/timeline/`
* [ ] hook frontend `useRecordTemplate(specialty)`
* [ ] componente `<DynamicForm schema={template.schema_json}>` para renderizar campos
* [ ] componente `<RecordTimeline>` com lazy loading
* [ ] testes de validação dinâmica

Detalhamento em `docs/prontuario.md` e `docs/templates-clinicos.md`.

---

## Templates seed por especialidade

* [ ] medicina geral — SOAP padrão + anamnese genérica
* [ ] odontologia — anamnese odontológica + ficha clínica (odontograma vem na Sprint 04)
* [ ] fisioterapia — avaliação funcional + evolução por sessão
* [ ] psicologia — anamnese psicológica + evolução com `is_private`
* [ ] biomedicina — solicitação e entrega de exame
* [ ] dermatologia — anamnese dermato + descrição de lesão padronizada
* [ ] estética — anamnese estética + checklist de contraindicações
* [ ] nutrição — anamnese nutricional + recordatório alimentar

---

## Prescrições, atestados e laudos

* [ ] modelo `Prescription` (genérica) + `PrescriptionItem`
* [ ] modelo `Attestation` (atestado)
* [ ] tipos de prescrição: sistêmica, tópica (dermato), nutricional, fisioterapêutica
* [ ] endpoint `POST /api/v1/prescriptions/`
* [ ] geração de PDF com WeasyPrint
* [ ] assinatura digital simples (Fase 1) — assinatura visível com nome + CRM
* [ ] templates de PDF parametrizados por branding do tenant
* [ ] envio do PDF ao paciente via portal e WhatsApp
* [ ] tela de prescrição com autocomplete de medicamentos (lista interna inicial)
* [ ] testes de geração de PDF

---

## Financeiro essencial

* [ ] modelo `FinancialAccount` (contas a receber, contas a pagar)
* [ ] modelo `Charge` (cobrança ligada a consulta/pacote)
* [ ] modelo `Payment` (pagamento confirmado)
* [ ] modelo `PaymentMethod` (PIX, cartão, dinheiro, boleto)
* [ ] adapter `PaymentGateway` (`MercadoPagoGateway` implementado)
* [ ] integração Mercado Pago — criar cobrança PIX
* [ ] integração Mercado Pago — criar cobrança cartão (link)
* [ ] webhook de confirmação de pagamento
* [ ] cobrança automática na conclusão da consulta
* [ ] cobrança manual avulsa
* [ ] recibo em PDF (WeasyPrint)
* [ ] tela de caixa do dia
* [ ] tela de contas a receber com status
* [ ] testes de webhook (idempotência)

Detalhamento em `docs/billing.md`, `ADRs/009-pagamentos-mercadopago-stripe.md`.

---

## Dashboards operacionais

* [ ] dashboard "Meu dia" (agenda + caixa + pacientes em espera)
* [ ] dashboard "Produção" (consultas concluídas no mês, ticket médio)
* [ ] dashboard "Financeiro" (recebido, a receber, inadimplência)
* [ ] componentes reusáveis: `<KPICard>`, `<SimpleChart>` (Recharts)
* [ ] permissões por dashboard via RBAC

---

## Auditoria reforçada

* [ ] todas as edições de `MedicalRecord` registradas em audit log
* [ ] leitura de `RecordEntry` com `is_private=True` (psicologia) registrada
* [ ] acesso de usuários sem vínculo direto registrado e justificado
* [ ] visualização de audit log filtrável por paciente e profissional

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* clínica de teste tem profissionais de 3+ especialidades cadastrados
* 50+ consultas atendidas com prontuário preenchido em cada especialidade
* 100+ prescrições/atestados gerados em PDF
* PIX recebido em ambiente sandbox
* dashboard de caixa do dia operando
* fluxo completo: agendar → check-in → atender → prescrever → cobrar → receber

---

# 6. NÃO ESTÁ NO ESCOPO

* odontograma visual (Sprint 04)
* mapa corporal de lesões (Sprint 04)
* pacotes de tratamento (Sprint 04)
* upload e comparação antes/depois (Sprint 04)
* automações WhatsApp (Sprint 04)
* portal do paciente (Sprint 04)
* TISS e convênios complexos (Fase 2)
* CRM (Fase 2)

---

# 7. RISCOS

* schema dinâmico mal validado vira buraco de segurança
  * mitigação: serializer valida contra JSON Schema antes de persistir
* PDFs gerados sob demanda podem ficar lentos
  * mitigação: geração em Celery + cache de URL no R2
* webhook do Mercado Pago pode chegar duplicado
  * mitigação: idempotência por `event_id`
* templates por especialidade muito rígidos limitam clínica
  * mitigação: cada clínica pode estender templates globais (ver `docs/templates-clinicos.md`)

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando (incluindo testes de tenancy)
* code review aprovado
* validação manual em staging com clínica fictícia
* PDF gerado validado em 3+ visualizadores

---

# 9. PRÓXIMA SPRINT

Sprint 04 cobrirá:

* WhatsApp transacional completo (Evolution API)
* automações básicas (confirmação, lembrete, pós-consulta)
* portal do paciente
* componentes específicos: odontograma, mapa de lesões, antropometria, pacotes de tratamento
* upload e comparação antes/depois

Ver `tasks/sprint-04.md`.
