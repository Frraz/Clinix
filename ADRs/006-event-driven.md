# ADR 006 — Arquitetura orientada a eventos

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

Várias funcionalidades do Clinix reagem ao que acontece no sistema:

* envio de WhatsApp ao criar agendamento
* lembrete 24h antes da consulta
* cobrança preventiva D-3 do vencimento
* notificação ao liberar laudo
* alimentação do CRM ao no-show
* analytics em tempo real
* IA opcional sobre eventos

Implementar isso com acoplamento direto (service A chama service B chama service C) gera:

* services gigantes e acoplados
* dificuldade de adicionar nova reação sem editar código existente
* impossibilidade de configurar workflows pelo usuário final

---

# Decisão

Adotar arquitetura **event-driven** interna:

* domínio publica eventos quando algo significativo acontece
* listeners (síncronos ou assíncronos) reagem
* execuções pesadas vão para Celery
* motor de workflows (ver `docs/workflows.md`) consome esses eventos

---

# Eventos canônicos

Vocabulário controlado:

```
appointment.created
appointment.cancelled
appointment.no_show
payment.confirmed
exam.released
patient.created
whatsapp.message.received
...
```

Lista completa em `docs/workflows.md`.

---

# Implementação

## MVP

* Django signals customizados como event bus
* Celery para execução assíncrona
* tabela de event log para auditoria e replay

---

## Evolução

* event bus dedicado (Redis Streams ou RabbitMQ) quando justificar
* migração não exige reescrita — a interface `EventBus.publish(event)` permanece

---

# Consequências

## Positivas

* baixo acoplamento entre módulos
* novos comportamentos adicionados sem editar fluxos existentes
* base natural para o motor de workflows
* base natural para integrações externas (webhooks)
* base natural para analytics em tempo real
* base natural para IA reativa

---

## Negativas

* depurar pode ficar menos óbvio que stack trace linear
* exige disciplina para não criar eventos demais (ruído)
* ordem de execução de listeners precisa ser definida

---

## Mitigações

* `request_id` propagado em todos os eventos
* logs estruturados correlacionam tudo
* curadoria do catálogo de eventos
* documentação obrigatória ao adicionar evento novo

---

# Garantias

* eventos são `at-least-once` (idempotência obrigatória em listeners)
* falhas em listeners não revertem a operação principal
* eventos críticos passam por dead-letter queue
* audit log registra publicação e consumo

---

# Alternativas consideradas

## Chamadas diretas entre services

Descartado: vira spaghetti em poucos meses. Inviabiliza o motor de workflows.

---

## Event Sourcing puro

Descartado: complexidade desproporcional para o MVP. O sistema continua persistindo estado tradicionalmente; eventos são notificações, não fonte de verdade.

---

## Kafka

Descartado para MVP: overkill. Redis Streams ou RabbitMQ cobrem quando precisarmos sair de signals.

---

# Referências

* `docs/workflows.md`
* `docs/arquitetura.md`
* `docs/auditoria.md`
