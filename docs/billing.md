# Billing — Clinix

> Estratégia de billing SaaS, planos, assinaturas e cobrança das clínicas que utilizam o Clinix.

---

# 1. VISÃO GERAL

O Clinix é um SaaS B2B vendido por assinatura mensal.

Este documento define billing do SaaS (cobrança das clínicas).

Para financeiro operacional das clínicas (cobrança de pacientes) ver `examples/fluxo-financeiro.md`.

---

# 2. OBJETIVOS

Garantir:

* cobrança recorrente automatizada
* upgrade/downgrade sem fricção
* trial sem cartão
* gestão de inadimplência
* visibilidade de receita
* expansão por add-ons

---

# 3. STACK

Tooling oficial:

* Stripe (assinaturas, faturas, dunning)
* Mercado Pago (alternativa BR — PIX para tenants brasileiros)
* webhooks de billing → Celery
* tabela própria de planos e features

---

# 4. MODELO DE COBRANÇA

Modelo principal:

# Assinatura mensal por clínica + add-ons

---

# Possíveis add-ons

* unidades extras
* usuários extras
* armazenamento extra
* WhatsApp Business oficial
* IA avançada
* integrações enterprise

---

# 5. PLANOS INICIAIS

## Starter

Para pequenas clínicas começando.

* 1 unidade
* 3 usuários
* agenda
* prontuário básico
* WhatsApp básico (limite mensal)
* financeiro essencial

---

## Professional

Para clínicas em crescimento.

* até 3 unidades
* 15 usuários
* todos os módulos do Starter
* CRM
* automações
* relatórios avançados
* WhatsApp ampliado

---

## Business

Para operações maiores.

* unidades ilimitadas
* usuários ilimitados
* tudo do Professional
* laboratório
* TISS
* BI avançado
* SLA prioritário

---

## Enterprise

Sob consulta.

* tenant dedicado
* infraestrutura isolada
* SSO
* customizações
* SLA enterprise
* gerente de conta

---

# 6. TRIAL

Trial padrão:

* 14 dias
* sem cartão
* todos os recursos do Professional
* limite seguro de WhatsApp

---

# 7. CICLO DE VIDA DO TENANT

Estados possíveis:

* `trial`
* `active`
* `past_due`
* `suspended`
* `cancelled`
* `churned`

---

# 8. UPGRADE / DOWNGRADE

Regras:

* upgrade aplicado imediatamente
* downgrade aplicado no próximo ciclo
* proration automática
* preview de fatura antes de confirmar

---

# 9. INADIMPLÊNCIA

Política de cobrança:

* D+0 → fatura emitida
* D+3 → lembrete
* D+7 → tentativa automática de cobrança
* D+10 → segundo aviso
* D+15 → suspensão preventiva (modo somente leitura)
* D+30 → cancelamento

Toda comunicação registrada em audit log.

---

# 10. SUSPENSÃO

Tenant suspenso:

* mantém acesso somente leitura por 30 dias
* não envia WhatsApp/email
* não permite novos agendamentos
* exibe banner global de pendência
* dados preservados

---

# 11. CANCELAMENTO

Ao cancelar:

* retenção de dados por 90 dias
* exportação completa disponibilizada
* anonimização após prazo legal
* respeito a LGPD

---

# 12. FATURAS

Toda fatura deve:

* ser enviada por email
* estar disponível no painel
* conter detalhamento de add-ons
* aceitar PIX, cartão e boleto

---

# 13. NOTA FISCAL

Emissão automática via:

* integração com NFS-e do município
* opção manual para municípios sem integração

---

# 14. WEBHOOKS DE BILLING

Eventos críticos:

* `invoice.created`
* `invoice.paid`
* `invoice.failed`
* `subscription.created`
* `subscription.updated`
* `subscription.cancelled`
* `payment_method.expired`

---

# 15. FEATURE FLAGS

Cada plano tem features:

```json
{
  "plan_slug": "professional",
  "features": {
    "modules": ["agenda", "prontuario", "financeiro", "crm", "automacoes"],
    "max_units": 3,
    "max_users": 15,
    "whatsapp_messages_per_month": 5000,
    "storage_gb": 50,
    "ai_enabled": false,
    "tiss_enabled": false
  }
}
```

---

# 16. LIMITES E QUOTAS

O sistema deve aplicar limites em tempo real:

* usuários ativos
* unidades cadastradas
* mensagens WhatsApp do mês
* storage utilizado
* execuções de automação

---

# 17. EXCEDENTES

Excedentes podem ser:

* bloqueados (Starter)
* cobrados por uso (Professional+)
* configuráveis pelo admin

---

# 18. RELATÓRIOS DE BILLING

O super-admin deve ter:

* MRR (Monthly Recurring Revenue)
* ARR (Annual Recurring Revenue)
* churn
* LTV
* CAC
* trial conversion
* uso por plano
* inadimplência

---

# 19. AUDITORIA DE BILLING

Toda mudança registrada:

* alteração de plano
* alteração de método de pagamento
* tentativas de cobrança
* suspensão
* cancelamento

---

# 20. SEGURANÇA

Nunca armazenar dados de cartão.

Tokenização via Stripe/MP.

---

# 21. WHITE-LABEL FUTURO

Arquitetura preparada para:

* revenda
* white-label
* split de receita
* gestão por parceiro regional

---

# 22. UPGRADE PATH

Roadmap de billing:

* fase 1: planos fixos + add-ons
* fase 2: cobrança por uso (WhatsApp, IA)
* fase 3: tiers anuais com desconto
* fase 4: contratos enterprise customizados

---

# 23. O QUE EVITAR

Nunca permitir:

* cobrança duplicada
* perda de evento de pagamento
* override silencioso de plano
* alteração de fatura paga
* armazenamento de cartão

---

# 24. OBJETIVO FINAL

Construir um billing SaaS:

* automatizado
* confiável
* transparente
* expansível
* preparado para crescer junto com o produto
