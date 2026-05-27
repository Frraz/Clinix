# Antropometria — Clinix

> Modelo, fluxo e cálculos antropométricos do Clinix. Usado por nutrição, estética e fisioterapia.

---

# 1. VISÃO GERAL

Antropometria é medição corporal usada para:

* nutrição: avaliação inicial e evolução
* estética: rastreamento de circunferências e composição
* fisioterapia: avaliação postural e estrutural
* medicina: acompanhamento metabólico

O módulo registra medidas padronizadas, calcula índices derivados (IMC, %GC) e gera gráficos de evolução.

---

# 2. OBJETIVOS

* registrar medidas com padronização
* permitir integração com bioimpedância
* calcular índices automáticos
* gerar gráficos de evolução
* exportar em PDF
* alimentar plano alimentar (nutrição)

---

# 3. PRINCÍPIOS

* unidades sempre metro/centímetro/kg (sem mistura)
* fórmulas registradas e auditáveis
* medidas históricas imutáveis
* paciente pode ver gráficos no portal (opt-in da clínica)

---

# 4. ENTIDADES

## AnthropometryRecord

```python
class AnthropometryRecord(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    record_entry = models.ForeignKey(RecordEntry, null=True, ...)
    measured_at = models.DateTimeField()

    # Básicos
    height_cm = models.DecimalField(max_digits=5, decimal_places=2)
    weight_kg = models.DecimalField(max_digits=6, decimal_places=2)

    # Circunferências (cm)
    circ_waist = models.DecimalField(null=True, ...)
    circ_hip = models.DecimalField(null=True, ...)
    circ_chest = models.DecimalField(null=True, ...)
    circ_abdomen = models.DecimalField(null=True, ...)
    circ_arm_relaxed = models.DecimalField(null=True, ...)
    circ_arm_contracted = models.DecimalField(null=True, ...)
    circ_thigh_proximal = models.DecimalField(null=True, ...)
    circ_thigh_distal = models.DecimalField(null=True, ...)
    circ_calf = models.DecimalField(null=True, ...)
    circ_neck = models.DecimalField(null=True, ...)

    # Dobras cutâneas (mm)
    skinfold_triceps = models.DecimalField(null=True, ...)
    skinfold_subscapular = models.DecimalField(null=True, ...)
    skinfold_suprailiac = models.DecimalField(null=True, ...)
    skinfold_abdominal = models.DecimalField(null=True, ...)
    skinfold_thigh = models.DecimalField(null=True, ...)

    # Composição corporal (calculados ou de bioimpedância)
    body_fat_pct = models.DecimalField(null=True, ...)
    muscle_mass_kg = models.DecimalField(null=True, ...)
    water_pct = models.DecimalField(null=True, ...)
    visceral_fat = models.IntegerField(null=True, ...)
    basal_metabolic_rate = models.IntegerField(null=True, ...)  # kcal

    # Origem dos dados
    has_bioimpedance = models.BooleanField(default=False)
    skinfold_formula = models.CharField(
        max_length=50, blank=True,
        choices=[("jackson_pollock_3", ...), ("jackson_pollock_7", ...),
                 ("durnin_womersley", ...), ("guedes", ...)]
    )

    notes = models.TextField(blank=True)
```

---

## BioimpedanceRecord

Registros vindos de balança de bioimpedância:

```python
class BioimpedanceRecord(TenantAwareModel):
    anthropometry = models.OneToOneField(AnthropometryRecord, ...)
    device = models.CharField(max_length=100, blank=True)
    raw_data = models.JSONField()  # dump do equipamento
    imported_at = models.DateTimeField(auto_now_add=True)
```

---

# 5. ÍNDICES CALCULADOS AUTOMATICAMENTE

## IMC

```
imc = weight_kg / (height_cm/100)²
```

Classificação:

| IMC | Classificação |
|-----|---------------|
| < 18.5 | abaixo do peso |
| 18.5–24.9 | eutrofia |
| 25–29.9 | sobrepeso |
| 30–34.9 | obesidade grau I |
| 35–39.9 | obesidade grau II |
| ≥ 40 | obesidade grau III |

---

## RCQ (Razão Cintura-Quadril)

```
rcq = circ_waist / circ_hip
```

Risco metabólico:

* mulheres: > 0.85 = risco aumentado
* homens: > 0.90 = risco aumentado

---

## % Gordura Corporal (fórmulas)

Implementadas:

* Jackson-Pollock 3 dobras (homens: peito, abdômen, coxa; mulheres: tríceps, suprailíaca, coxa)
* Jackson-Pollock 7 dobras
* Durnin-Womersley 4 dobras
* Guedes (validado para população brasileira)

A fórmula usada é registrada em `skinfold_formula`.

---

## TMB (Taxa Metabólica Basal)

Implementadas:

* Harris-Benedict
* Mifflin-St Jeor (padrão)
* FAO/WHO

Resultado armazenado em `basal_metabolic_rate` (kcal).

---

# 6. UI / FLUXO DE PREENCHIMENTO

Componente `<AnthropometryForm>` no frontend:

* campos agrupados por seção (Básico, Circunferências, Dobras, Bioimpedância)
* fórmula selecionada habilita só os campos necessários
* cálculos exibidos em tempo real
* importação de bioimpedância via upload CSV ou integração futura

---

# 7. EVOLUÇÃO

Gráficos de evolução temporal:

* peso e IMC
* % GC e massa muscular
* circunferências (linhas múltiplas)
* dobras (linhas múltiplas)

Componente `<EvolutionChart metric="weight">` etc.

Comparação ponto-a-ponto:

* diferença vs medição anterior
* diferença vs primeira medição
* meta (se definida)

---

# 8. METAS

Profissional pode definir metas:

```python
class AnthropometryGoal(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    metric = models.CharField(max_length=50)  # weight, body_fat_pct, etc
    target_value = models.DecimalField(...)
    target_date = models.DateField()
    set_by = models.ForeignKey(User, ...)
    set_at = models.DateTimeField(auto_now_add=True)
```

Sistema calcula:

* progresso (% atingido)
* ritmo necessário (resta X kg em Y dias)
* alerta se o ritmo está fora do saudável

---

# 9. INTEGRAÇÃO COM PLANO ALIMENTAR

* TMB calculada alimenta cálculo de calorias-alvo no plano alimentar (`docs/nutricao.md`)
* peso e composição alimentam recomendações de macros

---

# 10. INTEGRAÇÃO COM ESTÉTICA

Antropometria é usada em estética para:

* avaliação inicial (avaliação corporal)
* acompanhamento de pacote (drenagem, criofrequência)
* relatório antes/depois com medidas + fotos

---

# 11. INTEGRAÇÃO COM FISIOTERAPIA

* postura registrada em foto + medidas relevantes (cifose, lordose se aplicável)
* circunferência de membros para avaliar edema/atrofia

---

# 12. PORTAL DO PACIENTE

Paciente vê:

* última medição
* gráficos de evolução
* meta e progresso
* recomendações simples (sem diagnóstico)

Habilitação opcional pela clínica.

---

# 13. EXPORTAÇÃO

PDF de avaliação antropométrica com:

* tabela de medidas
* índices calculados
* gráfico de evolução
* assinatura do profissional
* branding do tenant

---

# 14. AUDITORIA

* `anthropometry.recorded`
* `anthropometry.updated`
* `anthropometry.goal_set`
* `anthropometry.goal_reached`

---

# 15. KPIs

* nº de avaliações por mês
* % de pacientes com evolução positiva
* tempo médio para meta
* taxa de aderência (medições consecutivas)

---

# 16. O QUE EVITAR

* salvar medições sem `measured_at` e `professional`
* mudar fórmula de uma medição salva
* permitir IMC sem altura/peso válidos
* exibir bioimpedância sem registrar device de origem
* extrapolar fórmula fora do range de idade/sexo válido

---

# 17. OBJETIVO FINAL

Engine antropométrica:

* precisa
* auditável
* visualmente clara
* integrada com nutrição, estética e fisioterapia
* alimentando metas e evolução do paciente

---

# 18. REFERÊNCIAS

* `docs/nutricao.md`
* `docs/estetica.md`
* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/portal-paciente.md`
* `examples/fluxo-plano-alimentar.md`
