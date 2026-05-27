# Fluxo de Agendamento — Clinix

> Jornada completa de um agendamento, desde a criação até o pós-atendimento.

---

# 1. PERSONAS ENVOLVIDAS

* paciente
* recepcionista
* profissional
* sistema (automações)

---

# 2. CANAIS DE ENTRADA

Um agendamento pode ser criado por:

* recepcionista no painel web
* paciente pelo portal
* paciente pelo WhatsApp (chatbot)
* paciente pelo app mobile (fase 4)
* integração com Doctoralia/Google Calendar (fase 5)

---

# 3. PRÉ-CONDIÇÕES

* tenant ativo e dentro dos limites do plano
* profissional cadastrado com agenda configurada
* paciente cadastrado (ou criado no momento)

---

# 4. CRIAÇÃO

## Passos

1. seleção do profissional, especialidade e unidade
2. consulta de horários disponíveis
3. seleção do horário
4. escolha ou cadastro do paciente
5. tipo de atendimento (`AppointmentType`)
6. observações opcionais
7. confirmação

---

## Validações

* horário ainda disponível (lock otimista)
* paciente sem conflito de agenda
* profissional sem conflito de agenda
* respeito a bloqueios e férias
* respeito a regras da especialidade

---

## Resultado

```json
{
  "appointment_id": 123,
  "status": "scheduled",
  "patient_id": 55,
  "professional_id": 10,
  "scheduled_start": "2026-06-01T14:00:00-03:00",
  "scheduled_end": "2026-06-01T14:30:00-03:00"
}
```

Evento publicado: `appointment.created`.

---

# 5. CONFIRMAÇÃO AUTOMÁTICA

Workflow padrão:

```yaml
trigger: appointment.created
actions:
  - delay: "until 48h before scheduled_start"
    action: whatsapp.send_template
    template: "confirmacao_consulta"
```

Mensagem enviada com botões:

* ✅ Confirmar
* 🔄 Reagendar
* ❌ Cancelar

---

# 6. RESPOSTA DO PACIENTE

## Confirmou

* status → `confirmed`
* evento `appointment.confirmed`
* recepção visualiza badge verde

---

## Reagendou

* sistema oferece próximos horários
* novo agendamento criado vinculado ao original
* evento `appointment.rescheduled`

---

## Cancelou

* status → `cancelled`
* registra motivo (do paciente)
* evento `appointment.cancelled`
* horário liberado na agenda
* fila de espera notificada (se houver)

---

# 7. LEMBRETE

Workflow:

```yaml
trigger: appointment.confirmed
actions:
  - delay: "until 24h before scheduled_start"
    action: whatsapp.send_template
    template: "lembrete_24h"
  - delay: "until 2h before scheduled_start"
    action: whatsapp.send_template
    template: "lembrete_2h"
```

---

# 8. CHECK-IN

Chegada do paciente na clínica:

* recepcionista marca check-in
* status → `waiting`
* evento `appointment.checked_in`
* paciente entra na fila do profissional
* opcional: senha digital

---

# 9. ATENDIMENTO

## Chamada

* profissional chama o próximo da fila
* status → `in_progress`
* prontuário abre automaticamente

---

## Durante

* evolução clínica registrada
* prescrições emitidas
* exames solicitados
* documentos gerados

Ver `examples/fluxo-prontuario.md`.

---

## Conclusão

* status → `completed`
* evento `appointment.completed`
* cobrança gerada (se aplicável)

---

# 10. PÓS-ATENDIMENTO

Workflows padrão:

```yaml
trigger: appointment.completed
actions:
  - delay: "2h"
    action: whatsapp.send_template
    template: "agradecimento"

  - delay: "24h"
    action: email.send
    template: "receita_e_orientacoes"

  - delay: "7 days"
    action: whatsapp.send_template
    template: "nps"

  - delay: "30 days"
    action: whatsapp.send_template
    template: "convite_retorno"
```

---

# 11. NO-SHOW

Se o paciente não chegou:

* recepcionista marca no-show (ou automação após X minutos)
* status → `no_show`
* evento `appointment.no_show`

Workflow:

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

# 12. CANCELAMENTO PELA CLÍNICA

Caso o profissional cancele:

* motivo obrigatório
* notificação automática ao paciente
* oferta de reagendamento prioritário
* audit log completo

---

# 13. REGRAS DE NEGÓCIO

## Conflitos

* nunca permitir dois agendamentos no mesmo horário/profissional
* lock otimista no banco evita race condition
* mensagem de erro clara

---

## Janela de cancelamento

* política por especialidade (ex: cancelar até 24h antes sem cobrança)
* configurável pela clínica
* exibida ao paciente

---

## Encaixes

* permitido apenas se a configuração da agenda permitir
* registrado como encaixe (não bloqueia horário regular)

---

# 14. PERMISSÕES

| Ação | Quem |
|------|------|
| Criar agendamento | recepcionista, gestor, paciente (portal) |
| Cancelar | recepcionista, gestor, profissional, paciente |
| Reagendar | recepcionista, gestor, paciente |
| Marcar no-show | recepcionista, gestor, profissional |
| Atender | profissional designado |

---

# 15. AUDITORIA

Registrar:

* criação do agendamento
* todas as mudanças de status
* quem fez cada mudança
* origem (web, mobile, WhatsApp, automação)

---

# 16. INDICADORES

KPIs gerados:

* taxa de no-show por profissional
* taxa de confirmação automática
* tempo médio de espera
* ocupação da agenda
* recuperação de faltosos

---

# 17. ESTADOS POSSÍVEIS

```txt
scheduled → confirmed → waiting → in_progress → completed
         ↘ cancelled
         ↘ no_show
         ↘ rescheduled
```

---

# 18. O QUE EVITAR

* permitir agendamento sem confirmação de disponibilidade
* alterar agendamento sem audit
* cancelar sem motivo
* sobrescrever horário em race condition
* sem notificação ao paciente em mudanças

---

# 19. OBJETIVO

Fluxo de agendamento que:

* reduz no-show
* economiza tempo da recepção
* melhora experiência do paciente
* alimenta CRM e analytics
* mantém auditoria completa
