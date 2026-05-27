# Sprint 04 — WhatsApp + Portal paciente + Componentes por especialidade

> Quarta sprint do Clinix. Fecha o MVP com WhatsApp, portal do paciente, automações básicas e componentes específicos das 8 especialidades.

---

# 1. OBJETIVO

Entregar:

* WhatsApp transacional completo via Evolution API
* automações operacionais (confirmação, lembrete, pós-consulta, recuperação)
* portal do paciente
* componentes específicos por especialidade que dependem de UI dedicada (odontograma, mapa de lesões, antropometria, pacotes, fotos antes/depois)
* finalização do MVP para todas as 8 especialidades-alvo

Ao fim desta sprint, o sistema está pronto para a primeira clínica piloto de qualquer especialidade.

---

# 2. DURAÇÃO ESTIMADA

3–4 semanas.

---

# 3. PRÉ-REQUISITOS

Sprint 03 concluída:

* prontuário composable funcionando para todas as 8 especialidades via templates
* financeiro recebendo PIX e cartão
* dashboards operando

---

# 4. ESCOPO

## WhatsApp via Evolution API

* [ ] provisionar Evolution API em VPS dedicada
* [ ] adapter Django `WhatsAppService` consumindo Evolution
* [ ] modelo `WhatsAppMessage` (histórico in/out)
* [ ] modelo `WhatsAppTemplate` (templates aprovados versionados)
* [ ] task Celery `send_whatsapp(tenant_id, to, template, params)`
* [ ] webhook de recebimento → event bus
* [ ] webhook de status (entregue, lido) → atualiza `WhatsAppMessage`
* [ ] opt-out automático (mensagem "PARAR" / "SAIR")
* [ ] painel de conversas (central omnichannel inicial)
* [ ] envio de PDFs anexados (receita, atestado, plano alimentar)
* [ ] rate limiting por tenant
* [ ] testes de idempotência e fallback

Detalhamento em `docs/whatsapp.md`, `ADRs/008-whatsapp-evolution-api.md`.

---

## Automações operacionais

* [ ] motor de automações baseado em eventos canônicos (ver `docs/workflows.md`)
* [ ] automação "confirmação D-1" (D = data da consulta)
* [ ] automação "lembrete 2h antes"
* [ ] automação "pós-consulta + NPS curto"
* [ ] automação "recuperação de faltoso"
* [ ] automação "cobrança preventiva D-3 do vencimento"
* [ ] automação "resultado de exame disponível"
* [ ] painel de configuração da automação (ligar/desligar por tenant)
* [ ] log de execução de automações
* [ ] testes E2E de cada automação

---

## Portal do Paciente

* [ ] subdomínio `portal.{tenant}.clinix.app` (ou path `/portal`)
* [ ] login do paciente (CPF + senha ou OTP via WhatsApp)
* [ ] tela "minhas consultas" (próximas e histórico)
* [ ] agendamento self-service (disponibilidade dos profissionais)
* [ ] check-in digital (24h antes da consulta)
* [ ] pré-anamnese (formulário dinâmico por especialidade)
* [ ] download de receitas, atestados, laudos
* [ ] visualização do plano alimentar (nutrição)
* [ ] visualização de pacotes de tratamento em andamento
* [ ] pagamento via link
* [ ] consentimento LGPD digital
* [ ] testes de acesso restrito (paciente vê só seus dados)

Detalhamento em `docs/portal-paciente.md`, `examples/fluxo-portal-paciente.md`.

---

## Componentes específicos por especialidade

### Odontologia
* [ ] componente `<DentalChart>` (notação FDI, estados por dente)
* [ ] modelo `DentalChart` com `chart_state` JSONB
* [ ] modelo `DentalProcedure` (procedimentos planejados/realizados por dente)
* [ ] integração com plano de tratamento e cobrança

Detalhamento em `docs/odontograma.md`.

---

### Dermatologia
* [ ] componente `<BodyLesionMap>` (SVG corporal interativo)
* [ ] modelo `SkinLesionMap` com `lesions_json`
* [ ] vincular lesão a fotos clínicas e à dermatoscopia
* [ ] timeline de evolução por lesão

Detalhamento em `docs/dermatologia.md`.

---

### Estética
* [ ] modelo `TreatmentPackage` (pacote com N sessões)
* [ ] modelo `TreatmentPackageSession`
* [ ] modelo `BeforeAfterPhoto` (vinculada a sessão, com metadata)
* [ ] componente `<BeforeAfterSlider>`
* [ ] checklist de contraindicações por procedimento
* [ ] integração com cobrança (parcelamento por pacote)

Detalhamento em `docs/estetica.md`, `docs/pacotes-tratamento.md`.

---

### Nutrição
* [ ] modelo `NutritionPlan` com `plan_data` JSONB versionado
* [ ] modelo `AnthropometryRecord`
* [ ] modelo `BioimpedanceRecord`
* [ ] componente `<AnthropometryForm>`
* [ ] componente `<MealPlanEditor>`
* [ ] gráfico de evolução de peso/composição corporal
* [ ] envio do plano alimentar via WhatsApp/portal

Detalhamento em `docs/nutricao.md`, `docs/antropometria.md`.

---

### Psicologia
* [ ] aplicação digital de escalas validadas (PHQ-9, GAD-7, DASS-21)
* [ ] cálculo automático de score
* [ ] gráfico de evolução por escala
* [ ] alerta automático em score de risco
* [ ] audit log reforçado (leitura de `is_private` registra acesso)

Detalhamento em `docs/escalas-testes.md`.

---

### Fisioterapia
* [ ] modelo `TherapyPlan` (plano com N sessões)
* [ ] evolução por sessão (campos: dor, ROM, força, observação)
* [ ] cálculo de progresso
* [ ] alta clínica

(Reusa `TreatmentPackage` em parte. Ver `docs/pacotes-tratamento.md`.)

---

### Biomedicina / Laboratório (escopo MVP)
* [ ] solicitação de exames (interna ou externa)
* [ ] vincular resultado externo via upload de PDF
* [ ] entrega do laudo via portal e WhatsApp

(Operação laboratorial completa — coleta, triagem, processamento — fica para Fase 2.5. Ver `docs/laboratorio.md`.)

---

## Imagens clínicas (transversal)

* [ ] upload via presigned PUT para R2
* [ ] geração de thumbnails (200/800/2000 px) via Celery
* [ ] compressão WebP/AVIF
* [ ] consentimento LGPD por foto
* [ ] metadados (data, ângulo, profissional, procedimento)
* [ ] visualização lado a lado (antes/depois)
* [ ] retenção e anonimização

Detalhamento em `docs/imagens-clinicas.md`.

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* clínica piloto com 5+ profissionais de 3+ especialidades em produção
* 200+ consultas atendidas com prontuário completo
* 500+ mensagens WhatsApp enviadas com confirmação automática
* portal do paciente acessado por 50+ pacientes
* 50+ pacotes de tratamento em andamento (estética/odonto/fisio)
* 100+ fotos clínicas com consentimento registrado
* 30+ planos alimentares enviados (nutrição)
* MVP **vendável** para qualquer uma das 8 especialidades

---

# 6. NÃO ESTÁ NO ESCOPO

* TISS e convênios complexos (Fase 2)
* CRM com funil (Fase 2)
* IA clínica (Fase 3)
* mobile (Fase 4)
* telemedicina (Fase 4)
* marketplace de integrações (Fase 6)
* multi-unidade (Fase 2 — flag de feature)
* laboratório completo com coleta/processamento (Fase 2.5)

---

# 7. RISCOS

* Evolution API instável pode quebrar fluxos críticos
  * mitigação: fallback para email/SMS, monitoramento Sentry, instância isolada
* upload de fotos sem otimização degrada performance
  * mitigação: presigned PUT + Celery para thumbnail, WebP/AVIF obrigatório
* automações disparando em loop
  * mitigação: idempotência por `event_id`, rate limit por tenant
* componentes específicos por especialidade explodirem o bundle do frontend
  * mitigação: code splitting por rota/especialidade
* opt-out do WhatsApp não cumprido vira problema regulatório
  * mitigação: filtro central na task `send_whatsapp` consultando opt-out antes
* sigilo psicológico violado por acesso indevido
  * mitigação: RBAC reforçado + audit log em leitura, alerta em acesso fora do contexto

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando (incluindo isolamento, idempotência, RBAC)
* code review aprovado
* validação manual em staging
* fluxo E2E testado em pelo menos 1 clínica fictícia por especialidade

---

# 9. PRÓXIMA FASE

Após Sprint 04 concluída, o MVP está pronto. Próximos passos em `docs/roadmap.md`:

* Fase 1.5 — polimento com base no feedback da clínica piloto
* Fase 2 — convênios/TISS, multi-unidade, CRM, laboratório completo
* Fase 3 — IA clínica e operacional
* Fase 4 — mobile e telemedicina
