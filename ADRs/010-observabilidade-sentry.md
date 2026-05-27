# ADR 010 — Observabilidade com Sentry + Logs Estruturados

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix é um sistema operacional crítico para clínicas. Falhas podem:

* impedir agendamentos e atendimentos
* gerar prejuízo financeiro direto
* causar perda de prontuário (inaceitável)
* violar LGPD (incidentes precisam ser detectados rápido)

Precisamos de observabilidade que cubra:

* erros em produção (backend Django, frontend Next.js, mobile)
* performance de APIs e queries
* falhas em filas Celery
* falhas em integrações externas (WhatsApp, pagamentos, R2)
* rastreabilidade de incidentes
* alertas em SLA crítico

E precisa caber no custo do MVP — sem montar stack de observabilidade enterprise no dia 1.

---

# Decisão

Adotar **Sentry** como ferramenta principal de observabilidade, complementada por **logs estruturados JSON** e métricas leves:

* Sentry capturando erros e performance (backend + frontend + mobile)
* logs JSON estruturados via `structlog` propagados para stdout
* `request_id` correlacionando logs + erros + eventos
* alertas Sentry → Slack/Discord
* dashboards opcionais via Grafana Cloud (free tier) para métricas

---

# Justificativa

## Sentry

* gratuito até 5k erros/mês (suficiente para MVP)
* SDK Python e JavaScript maduros
* integra com Django, DRF, Celery, Next.js, React Native
* performance monitoring incluído
* breadcrumbs automáticos
* session replay opcional no frontend (para debug visual)
* PII scrubbing nativo (importante para LGPD)

---

## Logs estruturados

* `structlog` no Django produz JSON
* stdout vai para Docker logs → Loki/CloudWatch/etc no futuro
* `request_id` em cabeçalho propagado em todos os logs
* permite correlacionar request → log → erro Sentry → evento

---

## Por que não Datadog/New Relic agora

* custo desproporcional para MVP
* migração futura para stack mais robusta é viável (Sentry continua)

---

# Implementação

## Backend Django

* `sentry-sdk[django,celery]` configurado
* environments separados: `dev`, `staging`, `production`
* `before_send` filtra PII (CPF, telefone, dados de prontuário)
* sampling de transactions configurado (10% em prod)
* tasks Celery instrumentadas

---

## Frontend Next.js

* `@sentry/nextjs` configurado
* source maps enviados no build
* session replay com mascaramento de dados sensíveis (LGPD)

---

## Mobile (Fase 4)

* `@sentry/react-native` quando o app entrar em produção

---

## Logs

```json
{
  "timestamp": "2026-05-27T14:23:45Z",
  "level": "info",
  "event": "appointment.created",
  "tenant_id": "uuid",
  "user_id": "uuid",
  "request_id": "uuid",
  "appointment_id": "uuid",
  "duration_ms": 23
}
```

Padrão único em todo o backend.

---

## Métricas leves

* Celery Flower para filas
* Postgres `pg_stat_statements` para queries lentas
* Grafana Cloud free para dashboards de SLOs

---

## Alertas

* Sentry → webhook → Slack/Discord
* limiares definidos em `docs/performance.md`
* PagerDuty futuro (Fase Enterprise)

---

# Consequências

## Positivas

* observabilidade real desde o MVP, custo zero/baixo
* PII scrubbing protege LGPD
* `request_id` permite rastrear qualquer incidente ponta a ponta
* migração para stack maior preserva Sentry como camada de erro

---

## Negativas

* sampling pode esconder transações raras
* dependência de SaaS externo (Sentry) — ok para o escopo
* free tier pode estourar com crescimento (preço escala)

---

## Mitigações

* monitorar consumo do plano free
* projetar para migrar `before_send` em caso de upgrade
* logs JSON em stdout não dependem de Sentry — sempre disponíveis localmente

---

# Alternativas consideradas

## Datadog

Descartado para MVP: caro, overkill. Reavaliar em Fase Enterprise.

---

## New Relic

Descartado para MVP: igual ao Datadog.

---

## ELK próprio (Elasticsearch + Logstash + Kibana)

Descartado: carga operacional alta. Sentry + logs JSON cobre o MVP.

---

## Honeycomb

Considerado: ótima ferramenta de tracing, mas custo desproporcional para MVP. Reavaliar quando volume justificar.

---

# Referências

* `docs/performance.md`
* `docs/auditoria.md`
* `docs/backup-dr.md`
* `docs/stack.md`
