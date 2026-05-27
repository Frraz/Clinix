# Fluxo Financeiro — Clinix

> Jornada completa de cobrança e pagamento de um atendimento.

---

# 1. ESCOPO

Este fluxo cobre o **financeiro operacional da clínica** (cobrança de pacientes).

Para billing do SaaS (cobrança das clínicas pela assinatura) ver `docs/billing.md`.

---

# 2. PERSONAS

* recepcionista
* paciente
* profissional
* financeiro
* gestor
* sistema (automações)

---

# 3. ORIGEM DE UMA COBRANÇA

Uma cobrança pode nascer de:

* atendimento concluído
* procedimento avulso
* parcelamento de tratamento (odonto, estética)
* mensalidade (psicologia, fisio com pacote)
* orçamento aprovado
* lançamento manual

---

# 4. CRIAÇÃO

## Automática

Disparada por `appointment.completed`:

* identifica tabela de preços (particular, convênio)
* identifica desconto aplicável
* gera `accounts_receivable`
* publica evento `invoice.created`

---

## Manual

Recepcionista lança:

* paciente
* serviço
* valor
* desconto
* forma de pagamento prevista
* vencimento

---

# 5. ESTRUTURA DA COBRANÇA

```json
{
  "id": 456,
  "patient_id": 55,
  "appointment_id": 123,
  "category_id": 10,
  "amount": 250.00,
  "discount": 0,
  "interest": 0,
  "due_date": "2026-06-05",
  "status": "pending",
  "installments": 1,
  "payment_method": null,
  "created_at": "2026-06-01T15:00:00-03:00"
}
```

---

# 6. STATUS

* `pending`
* `partially_paid`
* `paid`
* `overdue`
* `cancelled`
* `refunded`

Cada transição gera audit log.

---

# 7. PAGAMENTO

## Formas suportadas

* PIX
* cartão de crédito
* cartão de débito
* dinheiro
* boleto
* link de pagamento
* transferência

---

## Fluxo de pagamento

1. recepcionista seleciona forma
2. valor confirmado
3. integração com gateway (Mercado Pago) gera intent
4. paciente paga
5. webhook do gateway confirma
6. status → `paid`
7. recibo gerado e enviado

---

## Pagamento parcial

* registra valor pago
* status → `partially_paid`
* saldo permanece em aberto

---

# 8. PARCELAMENTOS

Comum em odonto e estética:

* tratamento de R$ 4.000 em 8x
* gera 8 cobranças com vencimentos mensais
* cada uma com fluxo próprio
* inadimplência em uma não bloqueia as outras automaticamente (decisão do gestor)

---

# 9. RECIBOS

Toda confirmação gera:

* recibo PDF
* enviado por WhatsApp e email
* armazenado no R2
* vinculado ao paciente

---

# 10. NOTAS FISCAIS

Integração com NFS-e municipal:

* emissão automática após pagamento
* fallback manual quando município não suporta
* PDF anexado à cobrança

---

# 11. COBRANÇA PREVENTIVA

Workflow padrão:

```yaml
trigger: invoice.created
actions:
  - delay: "until 3 days before due_date"
    action: whatsapp.send_template
    template: "lembrete_vencimento"
```

Reduz inadimplência sem incomodar.

---

# 12. INADIMPLÊNCIA

Quando vence sem pagamento:

* status → `overdue`
* evento `invoice.overdue`

Workflow:

```yaml
trigger: invoice.overdue
actions:
  - delay: "0d"
    action: whatsapp.send_template
    template: "primeiro_aviso"
  - delay: "4d"
    action: whatsapp.send_template
    template: "segundo_aviso"
  - delay: "8d"
    action: crm.create_task
    title: "Cobrança escalada para financeiro"
```

Sempre amigável, nunca agressivo.

---

# 13. NEGOCIAÇÃO

Quando o paciente negocia:

* novo plano de pagamento
* desconto autorizado pelo gestor (com audit)
* cobrança original referenciada
* novo acordo gerado

---

# 14. CONVÊNIOS

Quando paciente atende por convênio:

* cobrança vai para o convênio (não para o paciente)
* envio via TISS (XML padronizado)
* aguarda glosa
* glosa parcial registrada
* repasse acompanhado

---

# 15. GLOSAS

Processo:

* convênio glosa item
* sistema registra motivo
* permite recurso
* dashboard de glosas para análise
* relatório por convênio

---

# 16. REPASSE A PROFISSIONAIS

Para profissionais com modelo de comissão:

* cálculo automático por procedimento
* deduções (impostos, custos compartilhados)
* relatório mensal
* pagamento ao profissional
* recibo

---

# 17. CONTAS A PAGAR

Outro lado do financeiro:

* fornecedores
* aluguel
* equipe
* impostos
* assinaturas

Com:

* vencimentos
* parcelamentos
* aprovação por alçada
* conciliação bancária (futuro)

---

# 18. FLUXO DE CAIXA

Dashboard diário:

* entradas confirmadas
* entradas previstas
* saídas confirmadas
* saídas previstas
* saldo
* projeção 30 dias

---

# 19. DRE

Relatório mensal:

* receita bruta
* deduções (impostos, glosas)
* custos diretos
* despesas operacionais
* lucro líquido
* margem

---

# 20. CONCILIAÇÃO BANCÁRIA

Futuro (Fase 2):

* import de extrato
* match automático com cobranças
* alerta de divergências

---

# 21. PERMISSÕES

| Ação | Quem |
|------|------|
| Visualizar cobrança | recepcionista, financeiro, gestor |
| Criar cobrança | recepcionista, financeiro |
| Receber pagamento | recepcionista, financeiro |
| Aplicar desconto | financeiro (com alçada), gestor |
| Cancelar cobrança | gestor (auditado) |
| Visualizar DRE | gestor |
| Exportar dados financeiros | gestor (auditado) |

---

# 22. AUDITORIA

Registrar:

* criação de cobrança
* alteração de valor
* aplicação de desconto
* mudança de status
* recebimento
* cancelamento
* exclusão (apenas soft delete)

Toda alteração crítica exige justificativa.

---

# 23. INDICADORES

KPIs essenciais:

* faturamento do dia/mês
* ticket médio
* inadimplência (%)
* recuperação de inadimplentes
* tempo médio de recebimento
* margem por especialidade
* produtividade por profissional

---

# 24. SEGURANÇA

* dados financeiros nunca expostos em rota pública
* PCI compliance via gateway (nunca armazenar cartão)
* logs reforçados
* MFA obrigatório para operações de alto valor
* alerta para padrões suspeitos

---

# 25. INTEGRAÇÕES

Atuais:

* Mercado Pago (PIX, cartão, boleto)
* Stripe (assinaturas SaaS)
* NFS-e municipal (quando disponível)

Futuras:

* Open Banking
* ERPs contábeis
* automação de impostos

---

# 26. O QUE EVITAR

* alteração silenciosa de cobrança
* cancelamento sem justificativa
* armazenamento de dados de cartão
* exposição de valores entre tenants
* cobrança automática duplicada
* relatórios sem `tenant_id`

---

# 27. OBJETIVO

Fluxo financeiro que:

* reduz inadimplência
* automatiza cobrança
* dá visibilidade real ao gestor
* respeita compliance fiscal
* economiza tempo do financeiro
