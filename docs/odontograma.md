# Odontograma — Clinix

> Estrutura, modelo de dados e fluxos do odontograma do Clinix.

---

# 1. VISÃO GERAL

Odontograma é o registro visual do estado de cada dente do paciente. Em odontologia, é o equivalente ao "mapa corporal" da dermatologia. É usado para:

* avaliação inicial
* planejamento de tratamento
* execução de procedimentos
* histórico de cada dente
* orçamento e cobrança
* comparação histórica

---

# 2. OBJETIVOS

* registrar o estado de cada dente individualmente
* visualização interativa (clique no dente)
* histórico versionado (estado em diferentes datas)
* integração com plano de tratamento e cobrança
* suporte a dentição permanente e decídua

---

# 3. PRINCÍPIOS

* **notação FDI** (ISO 3950) como padrão — universal
* **JSONB versionado** — cada alteração gera nova versão
* **dente é uma entidade lógica**, não uma tabela física
* **face do dente** rastreada quando aplicável (oclusal, vestibular, lingual, mesial, distal)

---

# 4. NOTAÇÃO FDI

Cada dente é identificado por 2 dígitos:

* 1º dígito = quadrante (1–4 permanente, 5–8 decíduo)
* 2º dígito = posição (1–8 ou 1–5)

```
Permanente — superior direito: 18 17 16 15 14 13 12 11
            superior esquerdo: 21 22 23 24 25 26 27 28
            inferior esquerdo: 31 32 33 34 35 36 37 38
            inferior direito:  48 47 46 45 44 43 42 41

Decíduo — superior direito: 55 54 53 52 51
          superior esquerdo: 61 62 63 64 65
          inferior esquerdo: 71 72 73 74 75
          inferior direito:  85 84 83 82 81
```

---

# 5. ENTIDADES

## DentalChart

```python
class DentalChart(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    chart_type = models.CharField(
        max_length=20,
        choices=[("permanent", ...), ("deciduous", ...), ("mixed", ...)]
    )
    state_json = models.JSONField()  # estado completo
    version = models.PositiveIntegerField(default=1)
    created_by = models.ForeignKey(User, ...)
    created_at = models.DateTimeField(auto_now_add=True)
```

Estrutura de `state_json`:

```json
{
  "teeth": {
    "11": {
      "status": "restored",
      "faces": {
        "oclusal": {"status": "restoration", "material": "composite"},
        "vestibular": {"status": "intact"},
        "lingual": {"status": "intact"},
        "mesial": {"status": "intact"},
        "distal": {"status": "intact"}
      },
      "notes": "restauração 2026-03-15"
    },
    "21": {
      "status": "missing",
      "extracted_at": "2024-08-10",
      "reason": "fratura coronária"
    },
    "36": {
      "status": "implant",
      "implant_date": "2025-11-20"
    }
  }
}
```

---

## DentalProcedure

Procedimentos planejados/executados por dente:

```python
class DentalProcedure(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    chart = models.ForeignKey(DentalChart, ...)
    tooth_number = models.CharField(max_length=2)  # "11", "36"
    face = models.CharField(max_length=20, blank=True)  # "oclusal", etc
    procedure_code = models.CharField(max_length=20)  # código interno ou TUSS
    procedure_name = models.CharField(max_length=200)
    status = models.CharField(
        choices=[("planned", ...), ("in_progress", ...),
                 ("completed", ...), ("cancelled", ...)]
    )
    estimated_sessions = models.PositiveIntegerField(default=1)
    completed_sessions = models.PositiveIntegerField(default=0)
    unit_price = models.DecimalField(...)
    treatment_plan = models.ForeignKey('TreatmentPlan', null=True, ...)
    professional = models.ForeignKey(User, ...)
    planned_at = models.DateTimeField()
    completed_at = models.DateTimeField(null=True)
    notes = models.TextField(blank=True)
```

---

## TreatmentPlan

Plano de tratamento agregando procedimentos:

```python
class TreatmentPlan(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    title = models.CharField(max_length=200)
    description = models.TextField()
    status = models.CharField(
        choices=[("draft", ...), ("approved", ...), ("in_progress", ...),
                 ("completed", ...), ("cancelled", ...)]
    )
    total_amount = models.DecimalField(...)
    approved_by_patient_at = models.DateTimeField(null=True)
    treatment_package_instance = models.ForeignKey(
        TreatmentPackageInstance, null=True, ...
    )
    created_at = models.DateTimeField(auto_now_add=True)
```

* paciente assina digitalmente o plano antes de iniciar
* parcelamento via `TreatmentPackageInstance` (ver `docs/pacotes-tratamento.md`)

---

# 6. STATUS POR DENTE

* `intact` — íntegro
* `cariously` — com cárie
* `restored` — restaurado
* `crowned` — coroado
* `bridge` — pôntico de ponte
* `implant` — implante
* `endodontic` — tratamento de canal
* `missing` — ausente
* `extracted` — extraído (com data)
* `decayed` — em deterioração
* `eruption_in_progress` — em erupção (decíduo/misto)

---

# 7. UI / COMPONENTE FRONTEND

`<DentalChart mode="edit|view">`:

* desenho 2D dos dentes (SVG vetorial)
* clicar em um dente abre painel de detalhes
* clicar em uma face permite especificar (oclusal, vestibular, etc.)
* códigos de cor por status
* alternar entre dentição permanente / decídua / mista
* legenda interativa

Renderização performante via React + SVG (sem canvas).

---

# 8. VERSIONAMENTO

* cada alteração no chart cria nova `DentalChart` (snapshot completo)
* versão anterior nunca é alterada
* permite comparação histórica lado a lado
* timeline visual: ver chart em qualquer data

---

# 9. INTEGRAÇÃO COM PRONTUÁRIO

Template `avaliacao_inicial_odonto` inclui campo `dental_chart` que aponta para um `DentalChart`:

* abrir a entrada de prontuário renderiza o chart no contexto
* edições no prontuário podem disparar criação de nova versão do chart

---

# 10. INTEGRAÇÃO COM PLANO DE TRATAMENTO

* selecionando um dente, o profissional adiciona procedimentos
* sistema agrega procedimentos em um `TreatmentPlan`
* plano é apresentado ao paciente para aprovação
* aprovação inicia o pacote financeiro (`TreatmentPackageInstance`)

---

# 11. INTEGRAÇÃO COM AGENDA

Ao agendar uma sessão de tratamento:

* sistema pergunta qual procedimento será executado
* `Appointment` referencia `DentalProcedure`
* execução marca `DentalProcedure.completed_sessions += 1`
* quando `completed_sessions == estimated_sessions`, status vira `completed` e atualiza o chart

---

# 12. INTEGRAÇÃO COM IMAGENS

* radiografia digital ou foto intraoral por dente
* `ClinicalImage.dental_tooth_number = "16"`
* timeline de imagens por dente
* comparação histórica visual

---

# 13. INTEGRAÇÃO COM FINANCEIRO

* cada `DentalProcedure` tem `unit_price`
* `TreatmentPlan.total_amount = sum(procedures)`
* parcelamento via `TreatmentPackageInstance`
* cobrança progressiva por sessão concluída ou antecipada

---

# 14. RELATÓRIOS

* receita por procedimento
* procedimentos mais comuns
* taxa de conclusão de plano
* ticket médio por paciente

---

# 15. PORTAL DO PACIENTE

Paciente vê:

* odontograma simplificado (status visual)
* plano de tratamento ativo
* procedimentos concluídos
* radiografias e fotos suas
* parcelas pagas e em aberto

---

# 16. ODONTOGRAMA INFANTIL (DECÍDUO E MISTO)

* dentição decídua usa códigos 51–85
* dentição mista permite ambos os tipos no mesmo chart
* faixa etária define visualização padrão
* erupção / esfoliação registrada com data

---

# 17. AUDITORIA

* `dental_chart.versioned`
* `dental_procedure.planned`
* `dental_procedure.completed`
* `treatment_plan.approved`
* `treatment_plan.completed`

---

# 18. KPIs

* planos aprovados / planos apresentados
* tempo médio de conclusão de plano
* receita média por plano
* procedimentos mais executados

---

# 19. EXPORTAÇÃO

* odontograma em PDF (snapshot atual + procedimentos)
* plano de tratamento em PDF (com orçamento + termo)
* histórico completo do paciente em PDF (LGPD)

---

# 20. O QUE EVITAR

* alterar `state_json` sem criar nova versão
* permitir status inválido (ex: dente "missing" + "restored" ao mesmo tempo)
* exibir chart sem o tipo de dentição declarado
* aprovar plano sem assinatura registrada do paciente
* sobrescrever procedimento concluído

---

# 21. OBJETIVO FINAL

Construir um odontograma:

* moderno e visualmente rico
* preciso (notação FDI completa)
* versionado e auditável
* integrado com prontuário, agenda, financeiro e imagens
* sólido para odontologia geral, ortodontia, implantodontia, estética dental

---

# 22. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/pacotes-tratamento.md`
* `docs/imagens-clinicas.md`
* `docs/funcionalidades.md`
* `docs/billing.md`
