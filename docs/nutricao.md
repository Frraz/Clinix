# Nutrição — Clinix

> Módulo de nutrição do Clinix. Anamnese, antropometria, planos alimentares e acompanhamento.

---

# 1. VISÃO GERAL

A nutrição tem operação centrada em:

* anamnese profunda (hábitos, alergias, histórico médico)
* antropometria e bioimpedância
* recordatório alimentar
* construção do plano alimentar
* envio do plano ao paciente
* acompanhamento periódico (mensal ou quinzenal)
* ajuste contínuo

O Clinix atende com prontuário composable + módulo de antropometria + editor de plano alimentar.

---

# 2. OBJETIVOS

* anamnese estruturada e completa
* antropometria + bioimpedância integradas
* editor de plano alimentar com cálculo de macros e calorias
* envio do plano via portal + WhatsApp
* gráficos de evolução
* lembretes automáticos
* acompanhamento configurável (pacote mensal)

---

# 3. PRINCÍPIOS

* **toda recomendação tem base no cálculo TMB + objetivo**
* **plano alimentar é versionado** — paciente sempre vê a versão atual
* **substituições alimentares disponíveis** — paciente pode trocar dentro do mesmo grupo
* **paciente recebe plano em formato simples** (PDF + visualização no portal)
* **evolução antropométrica é o KPI clínico**

---

# 4. FLUXO CLÍNICO

```txt
agendamento inicial
   ↓
anamnese nutricional completa
   ↓
antropometria + bioimpedância (se disponível)
   ↓
cálculo de TMB + GET + meta calórica
   ↓
construção do plano alimentar
   ↓
explicação ao paciente
   ↓
envio do plano (portal + WhatsApp PDF)
   ↓
acompanhamento mensal:
    nova antropometria
    revisão da aderência
    ajustes no plano (nova versão)
    envio atualizado
   ↓
relatório final / alta nutricional
```

---

# 5. ENTIDADES

## NutritionalAssessment

```python
class NutritionalAssessment(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    record_entry = models.ForeignKey(RecordEntry, ...)

    # Objetivo
    goal = models.CharField(
        choices=[("weight_loss", ...), ("weight_gain", ...),
                 ("muscle_gain", ...), ("maintenance", ...),
                 ("therapeutic", ...), ("performance", ...)]
    )
    target_weight_kg = models.DecimalField(null=True, ...)
    target_date = models.DateField(null=True)

    # Histórico clínico relevante
    chronic_conditions = models.JSONField(default=list)  # ["diabetes", "htn"]
    allergies = models.JSONField(default=list)  # ["lactose", "glúten"]
    intolerances = models.JSONField(default=list)
    medications = models.JSONField(default=list)

    # Hábitos
    sleep_hours = models.DecimalField(null=True, ...)
    water_intake_ml = models.PositiveIntegerField(null=True)
    physical_activity = models.CharField(max_length=200, blank=True)
    activity_level = models.CharField(
        choices=[("sedentary", ...), ("light", ...), ("moderate", ...),
                 ("active", ...), ("very_active", ...)]
    )

    # Recordatório
    food_recall = models.JSONField()  # ver estrutura

    # Antropometria vinculada
    anthropometry = models.ForeignKey(
        AnthropometryRecord, null=True, ...
    )

    assessed_at = models.DateTimeField(auto_now_add=True)
```

---

## NutritionPlan

```python
class NutritionPlan(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    assessment = models.ForeignKey(NutritionalAssessment, ...)
    version = models.PositiveIntegerField(default=1)
    status = models.CharField(
        choices=[("draft", ...), ("active", ...), ("superseded", ...)]
    )

    # Cálculos base
    basal_metabolic_rate = models.IntegerField()  # kcal
    total_energy_expenditure = models.IntegerField()  # kcal
    target_kcal = models.IntegerField()

    # Distribuição de macros
    protein_g = models.PositiveIntegerField()
    carbs_g = models.PositiveIntegerField()
    fat_g = models.PositiveIntegerField()
    fiber_g = models.PositiveIntegerField()

    plan_data = models.JSONField()  # ver estrutura

    notes_patient = models.TextField(blank=True)  # ao paciente
    notes_internal = models.TextField(blank=True)  # ao profissional

    created_at = models.DateTimeField(auto_now_add=True)
    sent_to_patient_at = models.DateTimeField(null=True)
    pdf_document = models.ForeignKey('Document', null=True, ...)
```

Estrutura de `plan_data`:

```json
{
  "meals": [
    {
      "name": "Café da manhã",
      "time": "07:00",
      "items": [
        {"food": "ovo cozido", "qty": 2, "unit": "unidades", "kcal": 140, "protein": 12, "carbs": 0, "fat": 10},
        {"food": "pão integral", "qty": 1, "unit": "fatia", "kcal": 80}
      ],
      "substitutions": [
        {"of": "pão integral", "by": ["tapioca 2 col", "aveia 3 col"]}
      ],
      "totals": {"kcal": 220, "protein": 14, "carbs": 18, "fat": 11}
    }
  ],
  "daily_totals": {"kcal": 1800, "protein": 130, "carbs": 180, "fat": 60},
  "recommendations": [
    "Hidratar com 2.5L de água/dia",
    "Evitar refrigerantes",
    "Atividade física 3x/semana"
  ]
}
```

---

## FoodCatalog

Base de alimentos disponíveis para montar o plano:

```python
class Food(models.Model):  # global, não-tenant
    name = models.CharField(max_length=200)
    aliases = models.JSONField(default=list)
    category = models.CharField(max_length=100)  # cereal, fruta, proteína, etc.
    unit_default = models.CharField(max_length=20)  # "g", "ml", "unidade"
    serving_size = models.DecimalField(...)  # gramas por unidade padrão
    kcal_per_100g = models.DecimalField(...)
    protein_g = models.DecimalField(...)
    carbs_g = models.DecimalField(...)
    fat_g = models.DecimalField(...)
    fiber_g = models.DecimalField(...)
    sodium_mg = models.DecimalField(null=True, ...)
    source = models.CharField(max_length=100)  # TACO, USDA, IBGE
```

Base inicial: TACO (Tabela Brasileira de Composição de Alimentos).

---

# 6. EDITOR DE PLANO ALIMENTAR

Componente `<MealPlanEditor>`:

* drag-and-drop de alimentos
* cálculo de macros em tempo real
* sugestões automáticas para bater a meta
* substituições editáveis
* templates de plano (low carb, mediterrâneo, vegetariano, etc.)
* validação: alergias e intolerâncias bloqueiam alimentos relacionados
* preview do PDF antes de enviar

---

# 7. CÁLCULOS

## TMB (Mifflin-St Jeor — padrão)

* homens: `10 × peso + 6.25 × altura - 5 × idade + 5`
* mulheres: `10 × peso + 6.25 × altura - 5 × idade - 161`

Implementadas: Harris-Benedict e FAO/WHO como opções alternativas.

---

## GET (Gasto Energético Total)

```
GET = TMB × fator de atividade
```

* sedentário: 1.2
* leve: 1.375
* moderado: 1.55
* ativo: 1.725
* muito ativo: 1.9

---

## Meta calórica

* perda de peso: GET - 500 kcal (até -750 com supervisão)
* manutenção: GET
* ganho: GET + 300–500 kcal

Ajustável manualmente pelo profissional.

---

## Distribuição de macros (padrões editáveis)

* proteína: 1.6–2.2g/kg (dependendo do objetivo)
* gordura: 25–30% das calorias
* carboidrato: o restante

---

# 8. ANTROPOMETRIA

Reusa `AnthropometryRecord`. Ver `docs/antropometria.md`.

* primeira consulta: medição completa
* consultas subsequentes: medição leve (peso + circunferências chave)
* gráficos de evolução

---

# 9. INTEGRAÇÃO COM TEMPLATES

Templates seed:

* `anamnese_nutricional` — completa
* `recordatorio_alimentar_24h` — recall das últimas 24h
* `recordatorio_frequencia` — frequência semanal
* `avaliacao_antropometrica` — campo `anthropometry`
* `plano_alimentar` — campo `meal_plan`
* `evolucao_nutricional` — consulta de retorno

Ver `docs/templates-clinicos.md`.

---

# 10. INTEGRAÇÃO COM AGENDA

* tipos de consulta: avaliação inicial, retorno mensal, sessão de pacote
* duração padrão configurada (avaliação: 60min, retorno: 30min)
* recorrência mensal opcional

---

# 11. INTEGRAÇÃO COM PACOTES

Pacotes comuns:

* "Acompanhamento Nutricional 6 meses" — 1 consulta/mês
* "Programa Emagrecimento 3 meses" — semanal
* "Reeducação Alimentar 1 ano" — mensal

Ver `docs/pacotes-tratamento.md`.

---

# 12. ENVIO DO PLANO AO PACIENTE

* PDF gerado com WeasyPrint, branding do tenant
* visualização interativa no portal
* link enviado por WhatsApp (URL assinada)
* notificação opcional por email

PDF inclui:

* cabeçalho com clínica + profissional + CRN
* refeições e horários
* substituições
* recomendações
* metas (calorias, macros)
* hidratação
* observações

---

# 13. PORTAL DO PACIENTE

Paciente vê:

* plano atual (versão ativa)
* histórico de planos
* gráfico de evolução antropométrica
* recordatório (preencher entre consultas)
* meta e progresso
* lembretes de pesagem

---

# 14. WHATSAPP

Templates específicos:

* `nutri_envio_plano` — plano alimentar disponível
* `nutri_lembrete_pesagem` — registrar peso semanalmente
* `nutri_motivacional` — manutenção da aderência
* `nutri_proxima_consulta` — lembrete

Ver `docs/whatsapp.md`.

---

# 15. ACOMPANHAMENTO ENTRE CONSULTAS

Paciente pode registrar via portal/WhatsApp:

* peso semanal
* humor / energia
* aderência ao plano (escala 1–5)
* fotos opcionais
* dúvidas

Profissional vê dashboard de aderência antes da próxima consulta.

---

# 16. IA (FASE 3)

Casos de uso planejados:

* sugestão automática de substituições compatíveis
* análise de aderência baseada em recordatório
* sugestão de ajuste no plano com base na evolução
* chatbot do paciente para dúvidas comuns

Sempre como **assistente do nutricionista**, nunca substituindo prescrição.

---

# 17. KPIs

* nº de planos ativos
* taxa de aderência média
* evolução média (kg perdidos/ganhos)
* taxa de retorno por consulta
* receita por paciente
* tempo médio para atingir meta

---

# 18. RELATÓRIOS

* evolução de peso/composição por paciente
* faturamento por profissional
* % pacientes em meta
* alertas: paciente sem retorno há > 60 dias

---

# 19. AUDITORIA

* `nutrition_assessment.created`
* `nutrition_plan.published`
* `nutrition_plan.sent_to_patient`
* `anthropometry.recorded`
* `food_recall.submitted`

---

# 20. COMPLIANCE PROFISSIONAL

* prescrição dietética exige CRN
* o sistema valida que o profissional tem `specialty=nutrition`
* CRN exibido em todo documento gerado

Ver `docs/compliance-conselhos.md`.

---

# 21. ALERGIAS E INTOLERÂNCIAS

* registradas na anamnese
* bloqueiam alimentos relacionados no editor de plano
* exibidas com destaque em todo prontuário
* compartilhadas via portal para que paciente lembre

---

# 22. EXEMPLOS DE FLUXOS

## Primeira consulta

1. Anamnese nutricional completa
2. Antropometria + bioimpedância (se disponível)
3. Cálculo TMB e GET
4. Definição de meta
5. Construção do plano (60min)
6. Explicação ao paciente
7. Envio do PDF
8. Próxima consulta agendada

## Retorno mensal

1. Pesagem rápida
2. Revisão de aderência (preenchida pelo paciente entre consultas)
3. Ajustes no plano
4. Nova versão enviada
5. Próxima consulta agendada

## Alta nutricional

1. Meta atingida e mantida 3 meses
2. Relatório final emitido
3. Plano de manutenção entregue
4. Lembrete de retorno em 6 meses

---

# 23. O QUE EVITAR

* prescrever plano sem antropometria base
* alterar plano ativo sem criar nova versão
* enviar plano sem CRN visível
* sugerir alimento que conflita com alergia registrada
* contar consulta sem registrar evolução

---

# 24. OBJETIVO FINAL

Módulo de nutrição:

* científico e seguro
* operacionalmente fluido
* envolvente para o paciente (portal + WhatsApp)
* baseado em dados (antropometria + aderência)
* preparado para IA-assistida no futuro

---

# 25. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/antropometria.md`
* `docs/pacotes-tratamento.md`
* `docs/portal-paciente.md`
* `docs/whatsapp.md`
* `docs/compliance-conselhos.md`
* `docs/ia.md`
* `examples/fluxo-plano-alimentar.md`
