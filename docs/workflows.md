# Workflows — Clinix

> Motor de automação, eventos internos e workflows operacionais do Clinix.

---

# 1. VISÃO GERAL

O motor de workflows é uma das maiores oportunidades de diferenciação do Clinix.

Permite à clínica criar automações sem código:

> "Quando X acontecer, faça Y, sob condição Z."

---

# 2. OBJETIVOS

Garantir:

* automações operacionais sem desenvolvimento
* redução drástica de trabalho manual
* respostas em tempo real a eventos
* extensibilidade para futuras integrações
* auditoria completa de execução

---

# 3. CONCEITOS

## Evento

Algo que aconteceu no sistema.

Ex: `appointment.created`, `payment.confirmed`, `exam.released`.

---

## Trigger

Define qual evento dispara um workflow.

---

## Condição

Filtro opcional aplicado antes da execução.

Ex: "apenas se especialidade = Odontologia".

---

## Ação

O que será executado quando o gatilho for atendido.

Ex: enviar WhatsApp, criar tarefa, atualizar campo, agendar follow-up.

---

## Workflow

Conjunto trigger + condições + ações.

---

# 4. PRINCÍPIOS

O motor deve priorizar:

* assincronia (Celery)
* idempotência
* observabilidade
* tolerância a falhas
* baixa carga cognitiva no editor visual

---

# 5. STACK

Componentes:

* Redis + Celery (execução)
* PostgreSQL (definição e logs)
* Django signals + event bus interno
* editor visual no frontend (Fase 2)

---

# 6. CATÁLOGO DE EVENTOS

## Pacientes

* `patient.created`
* `patient.updated`
* `patient.anonymized`

---

## Agenda

* `appointment.created`
* `appointment.confirmed`
* `appointment.cancelled`
* `appointment.rescheduled`
* `appointment.no_show`
* `appointment.checked_in`
* `appointment.completed`

---

## Prontuário

* `medical_record.created`
* `medical_record.updated`
* `prescription.issued`
* `exam.requested`
* `exam.released`

---

## Financeiro

* `invoice.created`
* `payment.confirmed`
* `payment.failed`
* `invoice.overdue`

---

## Laboratório

* `lab_order.created`
* `lab_sample.collected`
* `lab_result.released`

---

## Comunicação

* `whatsapp.message.received`
* `whatsapp.message.delivered`
* `whatsapp.message.failed`
* `whatsapp.opt_out`

---

## Sistema

* `user.created`
* `permission.changed`
* `tenant.suspended`

---

# 7. CATÁLOGO DE AÇÕES

## Comunicação

* enviar WhatsApp
* enviar email
* enviar SMS
* enviar notificação push

---

## Operação

* criar tarefa interna
* atribuir responsável
* criar agendamento
* atualizar status
* mover paciente em funil CRM

---

## Financeiro

* gerar cobrança
* aplicar desconto
* mudar status de pagamento

---

## Clínico

* criar lembrete clínico
* solicitar exame
* notificar profissional

---

## Integração

* chamar webhook externo
* registrar evento em audit

---

# 8. CONDIÇÕES SUPORTADAS

Operadores básicos:

* `==`, `!=`
* `>`, `<`, `>=`, `<=`
* `in`, `not_in`
* `contains`
* `between`
* `is_null`, `is_not_null`

---

# 9. CAMPOS DISPONÍVEIS

Cada evento expõe campos para condições:

```json
{
  "appointment": {
    "id": 123,
    "patient_id": 55,
    "professional_id": 10,
    "specialty": "Odontologia",
    "status": "confirmed",
    "scheduled_start": "2026-06-01T14:00:00-03:00"
  }
}
```

---

# 10. EXEMPLOS DE WORKFLOW

## Exemplo 1 — Confirmação de consulta

```yaml
trigger: appointment.created
conditions:
  - appointment.status == "scheduled"
actions:
  - delay: "until 48h before scheduled_start"
    action: whatsapp.send_template
    template: "confirmacao_consulta"
```

---

## Exemplo 2 — Recuperação de faltoso

```yaml
trigger: appointment.no_show
actions:
  - delay: "2h"
    action: whatsapp.send_template
    template: "reagendamento_amigavel"
  - delay: "24h"
    action: crm.move_to_funnel
    funnel: "recuperacao_faltoso"
```

---

## Exemplo 3 — Cobrança preventiva

```yaml
trigger: invoice.created
conditions:
  - invoice.amount > 200
actions:
  - delay: "until 3 days before due_date"
    action: whatsapp.send_template
    template: "lembrete_vencimento"
```

---

## Exemplo 4 — Follow-up clínico

```yaml
trigger: appointment.completed
conditions:
  - appointment.specialty == "Fisioterapia"
actions:
  - delay: "30 days"
    action: whatsapp.send_template
    template: "retorno_fisio_30d"
  - delay: "30 days"
    action: crm.create_task
    title: "Verificar evolução do paciente"
```

---

# 11. EXECUÇÃO

Pipeline:

```txt
Evento de domínio
   ↓
Event Bus
   ↓
Avaliador de triggers (encontra workflows aplicáveis)
   ↓
Avaliador de condições
   ↓
Celery task por ação (com delay se aplicável)
   ↓
Execução
   ↓
Audit log
```

---

# 12. DELAYS

Suportar:

* `immediate`
* `delay: 2h`, `delay: 3d`
* `until: <campo de data + offset>`
* `cron: 0 8 * * *`

Implementação via Celery ETA / Beat.

---

# 13. IDEMPOTÊNCIA

Toda ação deve ser idempotente:

* chave de idempotência por execução
* segunda execução não duplica efeito
* especialmente crítico em pagamentos e WhatsApp

---

# 14. RETRY

Política padrão:

* até 5 tentativas
* backoff exponencial (1m, 5m, 15m, 1h, 6h)
* dead-letter queue após esgotamento
* alerta para o tenant

---

# 15. AUDIT DE EXECUÇÃO

Cada execução registra:

* workflow_id
* trigger event
* condições avaliadas
* ações executadas
* status de cada ação
* timestamps
* erros

---

# 16. PAUSA E DESATIVAÇÃO

* workflows podem ser pausados
* execuções em andamento finalizam
* pausa não cancela tasks já agendadas (decisão de UX)

---

# 17. VERSIONAMENTO

Editar um workflow gera versão nova:

* execuções em andamento usam a versão antiga
* execuções novas usam a nova
* histórico preservado

---

# 18. TEMPLATES DE WORKFLOW

Clinix oferece templates prontos:

* confirmação automática
* lembrete de consulta
* recuperação de faltoso
* cobrança preventiva
* solicitação de retorno
* NPS pós-atendimento
* aniversário do paciente

A clínica ativa com 1 clique.

---

# 19. EDITOR VISUAL (FASE 2)

Interface no padrão:

* canvas drag-and-drop
* nós (trigger, condição, ação, delay)
* preview de execução
* simulação com dados reais
* logs em tempo real

---

# 20. PERMISSÕES

Quem pode criar workflows:

* admin
* gestor

Quem pode visualizar logs:

* admin
* gestor
* DPO (se tocar dados sensíveis)

---

# 21. LIMITES

Por plano:

* número de workflows ativos
* execuções/mês
* delays máximos
* destinos externos permitidos

---

# 22. SEGURANÇA

* workflows não podem acessar dados de outros tenants
* webhooks externos passam por allowlist
* assinaturas HMAC obrigatórias
* validação de payload

---

# 23. PERFORMANCE

* event bus assíncrono
* execução em workers Celery
* nenhuma operação síncrona bloqueia a API
* throttling por tenant em alto volume

---

# 24. INTEGRAÇÃO COM IA

Workflows podem chamar IA:

* resumir prontuário antes da consulta
* sugerir próxima ação
* personalizar mensagem

Sempre com auditoria.

---

# 25. INTEGRAÇÃO COM CRM

Workflows alimentam:

* funil de leads
* funil de retorno
* funil de inadimplência
* funil de pós-atendimento

---

# 26. EXEMPLOS DE PIPELINE COMPLETO

## Jornada pós-consulta

```txt
appointment.completed
   ↓ 2h
WhatsApp "obrigado por sua visita"
   ↓ 24h
Email com receita e orientações
   ↓ 7 dias
WhatsApp NPS
   ↓ se NPS < 7
Cria ticket interno para gestor
   ↓ 30 dias
WhatsApp solicitação de retorno
```

---

# 27. O QUE EVITAR

Nunca permitir:

* execução sem audit
* workflows cross-tenant
* loops infinitos (proteção contra recursão)
* operações destrutivas sem confirmação humana
* bypass de RBAC via workflow

---

# 28. OBJETIVO FINAL

Construir um motor de workflows:

* poderoso
* fácil de usar
* seguro
* auditável
* diferenciado no mercado regional
* base para evolução em IA + automação avançada
