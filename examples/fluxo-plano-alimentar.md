# Fluxo de Plano Alimentar — Clinix

> Jornada completa de avaliação nutricional, construção do plano alimentar e acompanhamento.

---

# 1. PERSONAS ENVOLVIDAS

* paciente
* nutricionista
* sistema (cálculos, envio, lembretes)

---

# 2. CANAIS DE ENTRADA

* consulta inicial agendada
* indicação médica
* portal do paciente (Fase 2: agendamento self-service)

---

# 3. PRÉ-CONDIÇÕES

* paciente cadastrado
* nutricionista cadastrada com `specialty=nutrition` e CRN válido
* templates de prontuário nutricional ativos (seed do Clinix)

---

# 4. ETAPA 1 — AVALIAÇÃO INICIAL

## Passos

1. paciente comparece (consulta de 60min)
2. nutricionista abre prontuário com template `anamnese_nutricional`
3. preenche:
   * objetivo (perda/ganho de peso, performance, terapêutico)
   * histórico clínico relevante (DM, HAS, etc.)
   * alergias e intolerâncias
   * uso de medicamentos
   * hábitos (sono, água, atividade)
   * recordatório alimentar 24h
4. abre subform de antropometria (template `avaliacao_antropometrica`)
5. registra peso, altura, circunferências, dobras
6. (se equipamento disponível) importa bioimpedância
7. sistema calcula IMC, RCQ, %GC, TMB automaticamente

## Resultado

* `NutritionalAssessment` criado
* `AnthropometryRecord` vinculado
* IMC, TMB, GET calculados
* `RecordEntry` salvos vinculados

---

# 5. ETAPA 2 — DEFINIÇÃO DE META

## Passos

1. nutricionista revisa GET (gasto energético total)
2. define meta calórica:
   * perda: GET - 500 kcal
   * manutenção: GET
   * ganho: GET + 300–500 kcal
3. define distribuição de macros (ex: 25% proteína, 30% gordura, 45% carboidrato)
4. configura preferências (vegetariano, sem lactose, halal, etc.)
5. define meta antropométrica (peso ou %GC) e data-alvo

## Resultado

* meta registrada em `AnthropometryGoal`
* alvo de calorias e macros prontos para o plano

---

# 6. ETAPA 3 — CONSTRUÇÃO DO PLANO ALIMENTAR

## Passos

1. nutricionista abre `<MealPlanEditor>` com alvo pré-preenchido
2. monta refeições (geralmente 5–6: café, lanche manhã, almoço, lanche tarde, jantar, ceia)
3. para cada refeição:
   * adiciona alimentos do `FoodCatalog` (base TACO)
   * define quantidade (g, ml, unidade)
   * sistema calcula kcal e macros em tempo real
4. adiciona substituições para flexibilidade
5. adiciona recomendações textuais (hidratação, atividade, alimentos a evitar)
6. valida que totais batem com a meta (sistema alerta se desvio > 10%)
7. preview do PDF (com branding da clínica + CRN)

## Validações

* alimentos com alergia registrada bloqueados (alerta vermelho)
* totais de macros dentro da faixa configurada
* refeições não-vazias
* horários sugeridos consistentes

## Resultado

* `NutritionPlan` salvo como `draft`

---

# 7. ETAPA 4 — REVISÃO COM O PACIENTE

## Passos

1. nutricionista compartilha tela
2. explica refeições e substituições
3. paciente pergunta dúvidas
4. ajustes finos
5. nutricionista publica plano (`status="active"`)
6. PDF gerado e arquivado em `Document`

## Resultado

* `NutritionPlan.status = "active"`
* PDF disponível
* `RecordEntry` com referência ao plano

---

# 8. ETAPA 5 — ENVIO AO PACIENTE

## Passos

1. sistema gera URL assinada do PDF (24h)
2. envia via WhatsApp (template `nutri_envio_plano`) com link
3. notifica também por email (se preferência)
4. push notification se mobile (Fase 4)
5. plano aparece no portal do paciente

## Conteúdo da mensagem

```
Oi {paciente},

Seu plano alimentar está pronto! 🥗

Acesse aqui: {link}

Qualquer dúvida, estou à disposição.

{nutricionista}, CRN {numero}
```

(sem expor dados clínicos no corpo)

---

# 9. ETAPA 6 — ACOMPANHAMENTO ENTRE CONSULTAS

## Paciente registra no portal/WhatsApp:

* peso semanal
* humor / energia
* aderência ao plano (escala 1–5)
* fotos opcionais (com consentimento)
* dúvidas

## Sistema:

* armazena cada registro
* gera gráfico de evolução
* alerta nutricionista em variações significativas (perda > 1kg/semana, etc.)
* envia lembretes:
  * pesagem semanal (segunda-feira)
  * lembrete de hidratação (opcional)
  * frase motivacional (`nutri_motivacional`)

---

# 10. ETAPA 7 — CONSULTA DE RETORNO

## Frequência

Mensal por padrão (configurável). Programas mais intensivos podem ser quinzenais.

## Passos

1. lembrete D-1 via WhatsApp (`nutri_proxima_consulta`)
2. paciente comparece
3. nutricionista revisa registros de aderência
4. nova antropometria (rápida: peso + circunferências chave)
5. atualiza gráfico de evolução
6. analisa aderência (recordatório acumulado)
7. ajusta plano:
   * se evolução boa, mantém ou ajuste fino
   * se ruim, investigar e ajustar
8. nova versão do plano (versão+1)
9. envio ao paciente

## Resultado

* nova `AnthropometryRecord`
* nova `NutritionPlan` (versão+1, anterior vira `status="superseded"`)
* `RecordEntry` de retorno

---

# 11. ETAPA 8 — META ATINGIDA / ALTA

## Quando

* meta antropométrica atingida + mantida por 3 meses
* objetivo terapêutico cumprido

## Passos

1. consulta de alta
2. relatório final em PDF:
   * evolução completa (gráficos)
   * fotos antes/depois (se autorizado)
   * plano de manutenção
   * orientações de longo prazo
3. WhatsApp celebratório
4. agendamento de retorno em 6 meses (manutenção)

---

# 12. INTEGRAÇÃO COM PACOTES

Se nutricionista vende pacote (ex: "Acompanhamento 6 meses"):

* cada consulta consome 1 sessão do pacote
* portal mostra progresso (3/6 consultas)
* parcelamento já configurado na venda
* renovação automática sugerida no final

Ver `examples/fluxo-pacote-tratamento.md`.

---

# 13. AUTOMAÇÕES OPERACIONAIS

* lembrete de pesagem semanal
* lembrete de próxima consulta D-1
* parabéns no aniversário
* "como está se sentindo?" entre consultas
* alerta interno para paciente sem retorno > 60 dias
* sugestão de upsell ("você se beneficiaria de antropometria + bioimpedância")

---

# 14. PORTAL DO PACIENTE

Paciente acessa:

* plano atual (interativo + PDF)
* histórico de planos
* gráfico de evolução
* meta e progresso
* recordatório semanal
* registro de peso
* receitas saudáveis (Fase 2)

---

# 15. AUDITORIA

Eventos:

* `nutritional_assessment.created`
* `anthropometry.recorded`
* `nutrition_plan.draft_created`
* `nutrition_plan.published`
* `nutrition_plan.sent_to_patient`
* `nutrition_plan.updated`
* `nutrition_goal.set`
* `nutrition_goal.reached`

---

# 16. KPIs

* nº de planos ativos
* taxa de aderência média (1–5)
* evolução média (kg perdidos/ganhos)
* % de pacientes em meta
* taxa de retorno (mensal)
* receita por paciente

---

# 17. COMPLIANCE

* CRN visível em todo documento gerado
* plano alimentar é considerado prescrição dietética — apenas nutricionista habilitado
* registro em audit log de toda emissão de plano

Ver `docs/compliance-conselhos.md`.

---

# 18. ALERGIAS — TRATAMENTO ESPECIAL

* registradas como `Patient.allergies` e em `NutritionalAssessment.allergies`
* exibidas com destaque vermelho em todo prontuário
* bloqueiam alimentos relacionados no editor
* impressas em todo PDF do plano (rodapé)

---

# 19. CENÁRIOS DE EXCEÇÃO

## Paciente para de responder

* sem registros há > 21 dias
* alerta interno
* nutricionista decide entrar em contato
* automação de recuperação dispara em D+30

## Plano não funciona

* paciente sem evolução em 2 consultas seguidas
* sistema sugere revisão profunda
* possível encaminhamento a médico (HAS, DM, hipotireoidismo)

## Plano abandonado

* paciente cancela
* `NutritionPlan.status = "cancelled"`
* dados preservados (LGPD)

---

# 20. EXEMPLO REAL

Cenário: paciente quer perder 8kg em 6 meses na **Clínica MEDRON** (com nutricionista).

1. **Consulta inicial** (semana 1)
   * antropometria: 78kg, 165cm, IMC 28.6 (sobrepeso)
   * TMB: 1.450 kcal | GET: 1.995 kcal | Meta: 1.500 kcal/dia
   * Plano: 5 refeições, 130g proteína, 165g carbo, 50g gordura
   * PDF enviado via WhatsApp
2. **Acompanhamento semana 1–4**
   * registra peso toda segunda
   * aderência 4/5 média
   * humor estável
3. **Retorno mês 1**
   * peso: 76.2kg (-1.8kg)
   * ajuste leve no plano
   * versão 2 enviada
4. **Retorno mês 2**
   * peso: 74.5kg (-3.5kg total)
   * mantém plano
   * versão 3 com novas receitas
5. **Mês 6**
   * peso: 70kg (meta atingida)
   * alta nutricional
   * plano de manutenção
   * retorno em 6 meses

---

# 21. REFERÊNCIAS

* `docs/nutricao.md`
* `docs/antropometria.md`
* `docs/templates-clinicos.md`
* `docs/portal-paciente.md`
* `docs/whatsapp.md`
* `docs/billing.md`
* `docs/compliance-conselhos.md`
* `examples/fluxo-pacote-tratamento.md`
