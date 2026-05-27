# ADR 009 — Pagamentos com Mercado Pago + Stripe

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix tem dois fluxos de pagamento distintos:

## 1. Cobrança das clínicas pelos seus pacientes (B2C dentro do SaaS)

* PIX (predominante no Brasil)
* boleto bancário
* cartão de crédito com parcelamento
* link de pagamento
* assinatura recorrente para pacotes de tratamento

## 2. Billing do SaaS (clínicas pagando a Clinix)

* assinatura mensal/anual recorrente
* trial e upgrade/downgrade de plano
* cobrança automática via cartão
* gestão de invoices, recibos, falhas e cobrança preventiva

Cada fluxo tem requisitos distintos. Forçar um único gateway para ambos compromete experiência ou custo.

---

# Decisão

Adotar arquitetura **dual-gateway**:

* **Mercado Pago** para cobrança operacional das clínicas (B2C)
* **Stripe** para billing recorrente do SaaS (B2B Clinix → clínica)

Ambos integrados via services dedicados no backend, expostos como adapter genérico.

---

# Justificativa

## Mercado Pago para B2C operacional

* nativo em PIX, boleto e cartão brasileiros
* parcelamento sem fricção
* taxas competitivas para volume regional
* split de pagamento (para futuro repasse médico)
* SDK Python maduro
* boleto integrado (essencial no Brasil)

---

## Stripe para billing SaaS

* melhor experiência mundial em assinaturas recorrentes
* gestão de trials, cupons, dunning, upgrade/downgrade out-of-the-box
* invoices e recibos prontos
* webhooks robustos para sincronizar estado
* portal do cliente embutido
* tax management e compliance internacional

---

## Por que não unificar

* Mercado Pago tem assinaturas, mas UX pior para SaaS
* Stripe não tem PIX brasileiro maduro nem boleto
* mistura compromete ambos os fluxos

---

# Implementação

## Camada de abstração

```python
class PaymentGateway(ABC):
    def create_charge(self, ...): ...
    def confirm(self, ...): ...
    def refund(self, ...): ...
    def handle_webhook(self, payload): ...

class MercadoPagoGateway(PaymentGateway): ...
class StripeGateway(PaymentGateway): ...
```

Service decide qual usar baseado no contexto (B2C operacional vs B2B billing).

---

## Webhooks

* endpoints separados por gateway
* validação de assinatura obrigatória
* idempotência por `event_id`
* todos os eventos viram `payment.*` no event bus interno

---

## Reconciliação

* job diário compara Mercado Pago/Stripe vs banco local
* alertas em divergências
* relatórios financeiros consolidam ambos

Detalhamento em `docs/billing.md` e `docs/convenios.md`.

---

# Consequências

## Positivas

* melhor UX em cada fluxo
* PIX/boleto fortes no operacional
* assinaturas SaaS robustas no billing
* abstração permite trocar gateway sem reescrever services
* compliance fiscal facilitado

---

## Negativas

* dois SDKs para manter
* reconciliação contábil mais complexa
* custos operacionais em dois provedores
* desenvolvedores precisam conhecer ambos

---

## Mitigações

* abstração `PaymentGateway` isola complexidade
* relatórios consolidados ocultam separação para o usuário final
* testes de contrato para webhook de ambos
* documentação clara sobre quando usar qual

---

# Alternativas consideradas

## Apenas Stripe

Descartado: experiência de PIX e boleto inferior. Adoção brasileira mais lenta.

---

## Apenas Mercado Pago

Descartado: billing recorrente do SaaS é mais limitado.

---

## Asaas / Gerencianet (PagarMe)

Considerados: boas alternativas ao Mercado Pago. Reavaliar em fase 2 se o custo justificar. Asaas tem split de pagamento robusto.

---

## Pagar.me

Descartado para MVP: menos features de SaaS billing que Stripe.

---

# Referências

* `docs/billing.md`
* `docs/convenios.md`
* `docs/regras-de-negocio.md`
* `examples/fluxo-financeiro.md`
* `docs/stack.md`
