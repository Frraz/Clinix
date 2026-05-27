# Escalas e Testes Validados — Clinix

> Catálogo, aplicação digital e fluxo de escalas e testes validados — primariamente para psicologia, mas reusável em outras especialidades.

---

# 1. VISÃO GERAL

Profissionais de psicologia e psiquiatria usam **escalas validadas** para avaliação objetiva de:

* depressão (PHQ-9, BDI)
* ansiedade (GAD-7)
* estresse e depressão combinados (DASS-21)
* qualidade de vida
* risco suicida (Columbia)
* outros

Outras especialidades também usam escalas:

* fisioterapia: escala visual analógica de dor (EVA), Oswestry
* nutrição: SCOFF (transtornos alimentares)
* medicina: PHQ-2 como triagem rápida

Esse módulo padroniza a aplicação digital, cálculo de scores, evolução e alertas.

---

# 2. OBJETIVOS

* aplicar escalas digitalmente (no consultório ou via portal/WhatsApp)
* calcular score automaticamente
* registrar evolução temporal
* alertar em scores de risco
* sigilo reforçado (escalas em psicologia são extremamente sensíveis)
* permitir aplicação remota antes da consulta (pré-anamnese)

---

# 3. PRINCÍPIOS

* **escalas são versionadas** — atualização do questionário não invalida aplicações antigas
* **score sempre auditado** — cálculo registrado, não confiar em frontend
* **sigilo reforçado em psicologia** — `is_private` no nível da aplicação
* **alerta automático em risco** sem expor conteúdo a quem não deve ver

---

# 4. ENTIDADES

## Scale (catálogo)

```python
class Scale(models.Model):  # global, não-tenant
    code = models.SlugField(unique=True)  # "phq9", "gad7", "dass21"
    name = models.CharField(max_length=200)
    short_name = models.CharField(max_length=20)
    version = models.PositiveIntegerField(default=1)
    purpose = models.TextField()  # uso clínico
    target_specialty = models.JSONField()  # ["psychology", "medical"]
    age_min = models.PositiveIntegerField(null=True)
    age_max = models.PositiveIntegerField(null=True)
    items_json = models.JSONField()  # questões
    scoring_json = models.JSONField()  # regra de score
    interpretation_json = models.JSONField()  # faixas e significados
    references = models.TextField()  # bibliografia
    is_active = models.BooleanField(default=True)
```

Estrutura de `items_json`:

```json
[
  {
    "id": "q1",
    "text": "Pouco interesse ou prazer em fazer as coisas",
    "options": [
      {"value": 0, "label": "Nunca"},
      {"value": 1, "label": "Vários dias"},
      {"value": 2, "label": "Mais da metade dos dias"},
      {"value": 3, "label": "Quase todos os dias"}
    ]
  }
]
```

Estrutura de `scoring_json`:

```json
{
  "method": "sum",
  "subscales": null,
  "reverse_items": []
}
```

Estrutura de `interpretation_json`:

```json
{
  "ranges": [
    {"min": 0, "max": 4, "label": "Mínima", "severity": "none"},
    {"min": 5, "max": 9, "label": "Leve", "severity": "mild"},
    {"min": 10, "max": 14, "label": "Moderada", "severity": "moderate"},
    {"min": 15, "max": 19, "label": "Moderadamente grave", "severity": "moderate_severe"},
    {"min": 20, "max": 27, "label": "Grave", "severity": "severe"}
  ],
  "risk_flags": [
    {"item_id": "q9", "value_gte": 1, "label": "Ideação suicida"}
  ]
}
```

---

## ScaleApplication

```python
class ScaleApplication(TenantAwareModel):
    scale = models.ForeignKey(Scale, ...)
    scale_version = models.PositiveIntegerField()
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    answers_json = models.JSONField()  # {"q1": 2, "q2": 1, ...}
    score = models.IntegerField()
    severity = models.CharField(max_length=30)
    interpretation = models.CharField(max_length=200)
    risk_flags = models.JSONField(default=list)
    applied_at = models.DateTimeField()
    applied_via = models.CharField(
        choices=[("in_session", ...), ("portal", ...), ("whatsapp", ...)]
    )
    is_private = models.BooleanField(default=True)  # padrão em psicologia
    record_entry = models.ForeignKey(RecordEntry, null=True, ...)
```

---

# 5. ESCALAS SEED

## Psicologia (essenciais para o MVP)

* **PHQ-9** — depressão (9 itens)
* **GAD-7** — ansiedade (7 itens)
* **DASS-21** — depressão, ansiedade, estresse (21 itens, 3 subescalas)
* **BDI-II** — depressão Beck (21 itens)
* **Columbia Suicide Severity Rating Scale (C-SSRS)** — risco suicida (curta)

## Fisioterapia

* **EVA** — escala visual analógica de dor (0–10)
* **Oswestry Disability Index** — incapacidade lombar
* **Tampa Scale of Kinesiophobia** — cinesiofobia

## Nutrição

* **SCOFF** — triagem de transtornos alimentares (5 itens)

## Medicina

* **PHQ-2** — triagem rápida de depressão (2 itens)

Outras escalas podem ser adicionadas via migration ou via admin Clinix.

---

# 6. APLICAÇÃO

## Em consultório

* profissional abre escala no template de prontuário
* paciente responde em tablet ou o profissional pergunta
* score calculado em tempo real
* salvo como `ScaleApplication`

---

## Via portal do paciente

* paciente recebe link via WhatsApp/email
* responde antes da consulta (pré-anamnese)
* resultado disponível ao profissional ao abrir o prontuário

---

## Via WhatsApp

* mensagens com botões interativos
* paciente responde item por item
* resultado consolidado ao final

---

# 7. CÁLCULO DE SCORE

Backend (Python) é a fonte de verdade:

```python
def calculate_score(scale: Scale, answers: dict) -> dict:
    method = scale.scoring_json["method"]
    if method == "sum":
        score = sum(answers.values())
    elif method == "subscales":
        score = {sub: sum(answers[i] for i in items)
                 for sub, items in scale.scoring_json["subscales"].items()}
    severity = find_severity(scale, score)
    flags = detect_risk_flags(scale, answers)
    return {"score": score, "severity": severity, "risk_flags": flags}
```

Frontend mostra score em tempo real mas **nunca persiste sem revalidação no backend**.

---

# 8. ALERTAS DE RISCO

Quando `risk_flags` contém item crítico (ex: ideação suicida no PHQ-9 item 9):

* alerta visual destacado ao profissional
* event bus dispara `scale.risk_detected`
* automação opcional: notificação ao profissional supervisor
* nunca enviar alerta para paciente

---

# 9. EVOLUÇÃO

Gráfico de evolução por escala:

* eixo X: data
* eixo Y: score
* faixas de severidade coloridas como faixas de fundo
* tooltip com interpretação detalhada
* comparação entre escalas (ex: PHQ-9 + GAD-7 lado a lado)

---

# 10. SIGILO E ACESSO

* `is_private=True` por padrão em psicologia
* apenas profissional aplicador e o paciente vê (paciente vê o seu resultado, sem interpretação clínica detalhada se a clínica preferir)
* leitura por outro profissional só com `break_glass` justificado
* admin do tenant nunca vê conteúdo das respostas, apenas estatísticas agregadas
* audit log de leitura registrado

---

# 11. INTEGRAÇÃO COM PRONTUÁRIO

Template `escala_phq9` (ou genérico) inclui campo `scale_form` apontando para o `Scale.code`:

* renderiza o formulário interativo
* ao submeter, cria `ScaleApplication`
* score é incluído na entrada de prontuário

---

# 12. PORTAL DO PACIENTE

Paciente vê:

* escalas aplicadas a ele
* resultado e interpretação geral (não-clínica)
* gráfico simplificado da evolução
* link para preencher escala pendente

Habilitação opcional pela clínica (algumas preferem não mostrar score numérico).

---

# 13. EXPORTAÇÃO

* relatório PDF por escala (cabeçalho clínico + tabela de respostas + interpretação)
* histórico completo por paciente (LGPD)
* dados agregados anonimizados para pesquisa (opt-in da clínica + paciente)

---

# 14. AUDITORIA

* `scale.applied`
* `scale.viewed`
* `scale.risk_detected`
* `scale.exported`

Todos com `tenant_id`, `patient_id`, `professional_id`, `request_id`.

---

# 15. KPIs

* nº de aplicações por mês
* % de pacientes com escala aplicada
* tempo médio até nova aplicação (recorrência)
* taxa de melhora (variação de score entre aplicações)

---

# 16. VALIDAÇÃO CIENTÍFICA

Cada escala traz:

* referências bibliográficas
* validação em português brasileiro quando aplicável
* faixas de severidade conforme literatura
* idade-alvo validada

Atualizações de versão preservam aplicações antigas (`scale_version` snapshot).

---

# 17. EXPANSÃO FUTURA

* testes projetivos digitais (limitado pelo CFP — ver `docs/compliance-conselhos.md`)
* escalas de qualidade de vida (SF-36, WHOQOL-BREF)
* escalas pediátricas
* IA sugerindo escala apropriada com base na anamnese
* aplicação via voz no app mobile

---

# 18. O QUE EVITAR

* aplicar escala fora da idade-alvo sem alerta
* mostrar interpretação clínica sensível para paciente sem mediação
* permitir edição de respostas após salvar (criar nova aplicação se necessário)
* deletar `ScaleApplication` sem audit log
* expor `risk_flags` a quem não deve ver

---

# 19. OBJETIVO FINAL

Engine de escalas:

* cientificamente válida
* digitalmente fluida
* sigilosa por padrão
* alertando risco quando preciso
* integrada com prontuário e portal

---

# 20. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/compliance-conselhos.md`
* `docs/lgpd.md`
* `docs/portal-paciente.md`
* `docs/auditoria.md`
