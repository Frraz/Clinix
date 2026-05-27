# ADR 008 — WhatsApp via Evolution API

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

WhatsApp é pilar central do Clinix:

* confirmação automática de consulta
* lembrete D-1 e D-0
* pós-consulta e pesquisa de satisfação
* cobrança preventiva e inadimplência
* recuperação de faltosos
* envio de receita, laudo e plano alimentar
* central omnichannel (atendimento humano)
* automações por workflow

A solução escolhida precisa ser:

* compatível com a API oficial do WhatsApp (Business Platform / Cloud API)
* econômica para o ticket regional (R$497–R$997/mês por clínica)
* capaz de operar muitos números simultâneos (multi-tenant)
* programável (webhooks, envio, templates, mídia)
* compatível com automações via Celery

---

# Decisão

Adotar a **Evolution API** como camada de integração WhatsApp do Clinix:

* self-hosted em VPS própria (separada do servidor principal)
* conecta com WhatsApp via Cloud API oficial ou via web (modo não-oficial só em dev)
* expõe REST API consumida pelo backend Django via tasks Celery
* recebe webhooks de eventos (mensagem recebida, status de entrega)
* gerencia múltiplos números (1 por tenant) com isolamento

---

# Justificativa

## Custo

* gratuita e open-source
* sem mensalidade por número (apenas custo de infra)
* sem taxa por mensagem enviada via terceiro
* taxa do WhatsApp Cloud API repassada direto (sem markup)

## Funcionalidade

* templates aprovados
* envio de mídia (imagens, PDFs, áudios)
* leitura de status (entregue, lido)
* groups e listas
* automações via webhooks

## Operação

* sob nosso controle — sem dependência de SLA de terceiro
* fácil de escalar horizontalmente
* compatível com nossos padrões Docker/Nginx

## Compatibilidade futura

* migrar para WhatsApp Cloud API oficial direta no futuro é trivial (mesma semântica)

---

# Implementação

## Topologia

```
[Backend Django] → Celery task → [Evolution API VPS] → WhatsApp Cloud API → cliente
                                       ↓
                              webhook → [Backend Django] → event bus
```

---

## Convenções

* 1 instância Evolution por tenant
* fila Celery dedicada `whatsapp` com rate limiting por tenant
* templates aprovados versionados no Clinix
* todos os envios passam por audit log
* opt-out automático por mensagem do usuário

Detalhamento em `docs/whatsapp.md`.

---

# Consequências

## Positivas

* custo marginal por mensagem mínimo
* controle total da operação
* sem dependência de Twilio/360Dialog
* fácil de customizar para automações novas

---

## Negativas

* responsabilidade operacional sobre Evolution API (uptime, atualizações)
* risco de bloqueio do WhatsApp se usado fora dos templates aprovados
* curva de aprendizado da configuração

---

## Mitigações

* monitoramento (Sentry + Grafana) da Evolution
* uso obrigatório de templates aprovados
* opt-out e LGPD cumpridos rigorosamente
* fallback para SMS/email em caso de falha persistente do WhatsApp

---

# Alternativas consideradas

## Twilio WhatsApp API

Descartado: custo significativamente maior por mensagem. Inviável no ticket regional do Clinix.

---

## 360Dialog

Descartado: bom serviço, mas mais caro que Evolution self-hosted; sem controle total.

---

## Z-API

Descartado: solução brasileira boa, mas não-oficial (modo web), com risco de bloqueio. Evolution suporta ambos os modos.

---

## WhatsApp Cloud API direto da Meta

Considerado: viável tecnicamente, mas Evolution adiciona camada útil de multi-tenancy, templates e webhooks unificados. Migrar para a API direta no futuro permanece possível.

---

# Referências

* `docs/whatsapp.md`
* `docs/notificacoes.md`
* `docs/workflows.md`
* `docs/stack.md`
