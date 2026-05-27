# Pacotes de Tratamento — Clinix

> Estrutura, fluxo e integrações dos pacotes de tratamento (estética, odontologia, fisioterapia) no Clinix.

---

# 1. VISÃO GERAL

Várias especialidades trabalham com **tratamentos compostos por múltiplas sessões**:

* **estética** — pacote de 10 sessões de criofrequência, drenagem etc.
* **odontologia** — plano de tratamento ortodôntico (24+ sessões), implantes em etapas
* **fisioterapia** — plano de reabilitação com 20 sessões
* **dermatologia** — protocolo de 6 sessões de laser
* **nutrição** — acompanhamento mensal (cada consulta consome uma "sessão" do pacote)

Forçar essas operações como "consultas avulsas" perde:

* visão de progresso (4/10 sessões)
* parcelamento do valor total
* gestão de inadimplência por pacote
* histórico unificado do tratamento

Pacotes resolvem isso de forma reutilizável entre especialidades.

---

# 2. OBJETIVOS

* contratar um tratamento de N sessões com valor total
* parcelar o pagamento
* consumir sessões à medida que o paciente comparece
* visualizar progresso
* renovar / vender upsell
* relatórios de receita por pacote
* integração com financeiro e agenda

---

# 3. PRINCÍPIOS

* **um pacote tem valor e sessões** — ambos são contratos
* **uma sessão consumida não pode ser "des-consumida"** (mas pode ser anulada com motivo)
* **financeiro segue o pacote**, não a consulta
* **paciente vê o progresso** no portal
* **comissões / repasse** consideram o pacote vendido

---

# 4. ENTIDADES

## TreatmentPackage (catálogo)

```python
class TreatmentPackage(TenantAwareModel):
    specialty = models.ForeignKey(Specialty, ...)
    name = models.CharField(max_length=200)
    description = models.TextField()
    total_sessions = models.PositiveIntegerField()
    base_price = models.DecimalField(max_digits=10, decimal_places=2)
    expected_interval_days = models.PositiveIntegerField()  # 7, 14, 30...
    is_active = models.BooleanField(default=True)
```

* catálogo de pacotes disponíveis para venda
* a clínica define seus próprios

---

## TreatmentPackageInstance (venda)

Um pacote vendido a um paciente:

```python
class TreatmentPackageInstance(TenantAwareModel):
    template = models.ForeignKey(TreatmentPackage, ...)
    patient = models.ForeignKey(Patient, ...)
    primary_professional = models.ForeignKey(User, ...)
    total_sessions = models.PositiveIntegerField()  # snapshot
    sessions_consumed = models.PositiveIntegerField(default=0)
    total_amount = models.DecimalField(...)
    status = models.CharField(
        choices=[("active", ...), ("completed", ...), ("cancelled", ...), ("paused", ...)]
    )
    sold_at = models.DateTimeField()
    expected_completion = models.DateField()
    notes = models.TextField(blank=True)
```

---

## TreatmentPackageSession (sessão)

Cada sessão consumida:

```python
class TreatmentPackageSession(TenantAwareModel):
    instance = models.ForeignKey(TreatmentPackageInstance, ...)
    session_number = models.PositiveIntegerField()
    appointment = models.OneToOneField(Appointment, null=True)
    record_entry = models.OneToOneField(RecordEntry, null=True)
    professional = models.ForeignKey(User, ...)
    executed_at = models.DateTimeField(null=True)
    status = models.CharField(
        choices=[("scheduled", ...), ("done", ...), ("missed", ...), ("voided", ...)]
    )
    notes = models.TextField(blank=True)
```

* sessão vinculada a um agendamento e/ou a uma entrada de prontuário
* fotos antes/depois (estética) podem ser anexadas via `ClinicalImage.treatment_package_session`

---

# 5. STATUS DO PACOTE

* `active` — em uso
* `paused` — interrompido (motivo registrado)
* `completed` — todas as sessões consumidas
* `cancelled` — cancelado com reembolso

---

# 6. FLUXO TÍPICO

```
catálogo
   ↓
venda (instance criado)
   ↓
parcelamento (financeiro)
   ↓
agendamento da 1ª sessão
   ↓
execução (cria/atualiza TreatmentPackageSession)
   ↓
prontuário registrado
   ↓
foto antes/depois anexada (se aplicável)
   ↓
... (repete N vezes)
   ↓
conclusão (status=completed)
   ↓
upsell ou alta
```

---

# 7. INTEGRAÇÃO COM AGENDA

Ao agendar uma consulta para um paciente que tem um pacote ativo:

* sistema oferece "consumir 1 sessão do pacote X"
* se aceito, `Appointment.treatment_package_instance_id` é preenchido
* contagem é incrementada apenas ao concluir o atendimento

---

# 8. INTEGRAÇÃO COM FINANCEIRO

Detalhamento em `docs/billing.md`.

* venda do pacote gera `Charge` com `total_amount`
* parcelamento usa `installments` (PIX/boleto/cartão recorrente)
* inadimplência por pacote — paciente em atraso pode ter agendamento bloqueado (configurável)
* relatórios de receita por pacote, ticket médio por pacote, LTV

---

# 9. INTEGRAÇÃO COM PRONTUÁRIO

Cada sessão consumida:

* gera uma `RecordEntry` usando template específico (ex: `sessao_estetica`)
* template pode receber dados de fotos antes/depois automaticamente
* evolução fica vinculada ao histórico de sessões do pacote

---

# 10. RENOVAÇÃO E UPSELL

Quando uma sessão final é executada:

* sistema sugere renovação
* automação WhatsApp dispara após D+7 oferecendo continuidade
* histórico do pacote permanece vinculado para análise

---

# 11. PORTAL DO PACIENTE

Paciente vê:

* pacote ativo
* progresso (sessões consumidas)
* próximas sessões agendadas
* fotos antes/depois (se permitido)
* parcelas pagas e em aberto
* botão de agendar próxima sessão

---

# 12. PAUSA E CANCELAMENTO

* pausa: sessões pendentes congeladas, valor permanece
* cancelamento: motivo obrigatório, valor remanescente reembolsado conforme política

---

# 13. AGENDA RECORRENTE

Ao vender um pacote, opcional criar **N agendamentos automaticamente** com intervalo `expected_interval_days`:

* paciente confirma cada um via WhatsApp
* reagendamento individual permitido

---

# 14. AUDITORIA

Eventos registrados:

* `package.sold`
* `package_session.executed`
* `package_session.voided`
* `package.paused`
* `package.completed`
* `package.cancelled`

---

# 15. KPIs

* pacotes ativos por especialidade
* taxa de conclusão (% de pacotes que viram `completed`)
* receita por pacote
* taxa de no-show de sessão
* LTV por paciente
* tempo médio de conclusão (esperado vs real)
* taxa de renovação

---

# 16. EXEMPLOS POR ESPECIALIDADE

## Estética

* "Pacote Drenagem 10 sessões — R$ 1.500 em 6x"
* sessão registra fotos antes/depois
* automação WhatsApp lembra 24h antes

## Odontologia

* "Plano ortodôntico 24 meses — R$ 6.000 em 24x"
* cada sessão registra ajuste em `dental_chart`
* odontograma versionado a cada sessão

## Fisioterapia

* "Reabilitação pós-cirúrgica 20 sessões — R$ 2.000 em 4x"
* sessão registra ROM, dor, exercícios
* alta clínica encerra o pacote

## Dermatologia

* "Protocolo Laser 6 sessões — R$ 3.000 em 6x"
* sessão registra fotos da região tratada
* intervalo de 30 dias entre sessões

## Nutrição

* "Acompanhamento 6 meses — R$ 1.200 em 6x"
* cada consulta consome 1 sessão
* envio de plano alimentar atualizado a cada sessão

---

# 17. O QUE EVITAR

* consumir sessão sem registrar prontuário
* permitir agendamento de sessão extra além do contratado sem aviso financeiro
* pacote sem template de prontuário associado
* exclusão de pacote ativo sem fluxo de cancelamento
* mistura entre cobrança da sessão e cobrança do pacote

---

# 18. OBJETIVO FINAL

Suportar pacotes de tratamento de qualquer especialidade com:

* venda fluida
* parcelamento integrado
* progresso visível para paciente e clínica
* integração com prontuário, agenda, financeiro
* histórico completo para análise e renovação

---

# 19. REFERÊNCIAS

* `docs/billing.md`
* `docs/estetica.md`
* `docs/odontograma.md`
* `docs/imagens-clinicas.md`
* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `examples/fluxo-pacote-tratamento.md`
