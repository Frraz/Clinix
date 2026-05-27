# Dermatologia — Clinix

> Módulo de dermatologia do Clinix. Fluxo clínico, modelagem, integrações e templates.

---

# 1. VISÃO GERAL

A dermatologia exige recursos específicos:

* mapeamento corporal de lesões (mancha, nevo, verruga, etc.)
* dermatoscopia (foto + anotações)
* registro fotográfico padronizado
* prescrição de medicamentos sistêmicos e tópicos
* protocolos de seguimento de lesões suspeitas
* biópsia e acompanhamento de resultado

O Clinix atende esses requisitos via prontuário composable + componentes específicos.

---

# 2. OBJETIVOS

* registro visual rico do estado da pele do paciente
* rastreamento longitudinal de lesões
* anexo fotográfico padronizado com consentimento
* prescrição tópica diferenciada de sistêmica
* comunicação automatizada (orientações pós-procedimento, resultado de biópsia)
* integração com pacotes de tratamento (laser, dermoabrasão, etc.)

---

# 3. PRINCÍPIOS

* **toda lesão tem coordenada corporal**
* **toda foto clínica tem consentimento próprio**
* **lesão suspeita gera follow-up automático**
* **prescrição tópica é categoria à parte** (não usa receita controlada típica)
* **comparação histórica visual** é diferencial competitivo

---

# 4. FLUXO CLÍNICO

```txt
agendamento
   ↓
anamnese dermatológica
   ↓
exame físico
   ↓
mapeamento de lesões (body map)
   ↓
dermatoscopia (se aplicável)
   ↓
diagnóstico (CID + descrição)
   ↓
conduta (clínica / cirúrgica / biópsia)
   ↓
prescrição (tópica/sistêmica) + orientações
   ↓
agendamento de retorno
   ↓
WhatsApp com orientações pós-procedimento
   ↓
[se biópsia] aguardar resultado → registrar laudo → conduta
   ↓
seguimento periódico (rastreio de lesões suspeitas)
```

---

# 5. ENTIDADES

## SkinLesionMap

Mapa corporal versionado com lesões marcadas:

```python
class SkinLesionMap(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    lesions_json = models.JSONField()  # ver estrutura abaixo
    version = models.PositiveIntegerField(default=1)
    created_by = models.ForeignKey(User, ...)
    created_at = models.DateTimeField(auto_now_add=True)
```

Estrutura de `lesions_json`:

```json
{
  "lesions": [
    {
      "id": "lesion-uuid",
      "label": "L1",
      "body_view": "anterior",
      "x": 0.42,
      "y": 0.31,
      "morphology": "nevo",
      "color": "marrom",
      "size_mm": 4.5,
      "shape": "circular",
      "borders": "regular",
      "symmetry": "simétrico",
      "suspicion": "low",
      "first_observed": "2026-01-10",
      "image_ids": ["clinical-image-uuid-1", "clinical-image-uuid-2"],
      "abcde": {
        "asymmetry": false,
        "border_irregular": false,
        "color_variation": false,
        "diameter_gt_6mm": false,
        "evolving": false
      },
      "notes": "acompanhar em 6 meses"
    }
  ]
}
```

---

## Lesion (denormalizado para queries rápidas)

Cada lesão também tem registro próprio para facilitar busca:

```python
class Lesion(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    lesion_uid = models.UUIDField()  # mesmo id do JSON
    label = models.CharField(max_length=20)
    suspicion = models.CharField(
        choices=[("low", ...), ("moderate", ...), ("high", ...),
                 ("biopsied", ...), ("excised", ...), ("resolved", ...)]
    )
    morphology = models.CharField(max_length=50)
    first_observed = models.DateField()
    last_reviewed = models.DateField()
    needs_followup = models.BooleanField(default=False)
    followup_at = models.DateField(null=True)
```

---

## DermatoscopyNote

```python
class DermatoscopyNote(TenantAwareModel):
    lesion = models.ForeignKey(Lesion, ...)
    clinical_image = models.ForeignKey(ClinicalImage, ...)
    findings = models.TextField()
    pattern = models.CharField(max_length=100, blank=True)
    suspicion_level = models.CharField(max_length=20)
    professional = models.ForeignKey(User, ...)
    created_at = models.DateTimeField(auto_now_add=True)
```

---

## BiopsyOrder

```python
class BiopsyOrder(TenantAwareModel):
    lesion = models.ForeignKey(Lesion, ...)
    requested_by = models.ForeignKey(User, ...)
    requested_at = models.DateTimeField(auto_now_add=True)
    sent_to_lab = models.CharField(max_length=200, blank=True)
    status = models.CharField(
        choices=[("requested", ...), ("collected", ...),
                 ("sent_to_lab", ...), ("result_received", ...),
                 ("interpreted", ...)]
    )
    result_pdf = models.ForeignKey('Document', null=True, ...)
    result_text = models.TextField(blank=True)
    diagnosis_cid = models.CharField(max_length=10, blank=True)
    interpreted_at = models.DateTimeField(null=True)
```

---

# 6. UI / COMPONENTES

## BodyLesionMap

Componente SVG corporal interativo:

* vistas: anterior, posterior, lateral direita/esquerda, dorso da mão/pé, couro cabeludo
* clicar adiciona lesão no ponto
* arrastar reposiciona
* clicar em lesão existente abre painel
* códigos de cor por suspicion
* contagem por vista

---

## Dermatoscopy

* foto carregada da dermatoscopia
* anotações em sobreposição (SVG)
* checklist ABCDE
* gravação de padrão dermatoscópico

---

# 7. INTEGRAÇÃO COM IMAGENS CLÍNICAS

`ClinicalImage` em dermatologia recebe `category`:

* `clinical_overview` — visão clínica
* `dermatoscopy` — foto de dermatoscópio
* `treatment_progress` — evolução do tratamento

Vincula-se a `lesion_id` via `metadata`.

Detalhes em `docs/imagens-clinicas.md`.

---

# 8. PRESCRIÇÃO TÓPICA

Categoria de prescrição separada:

```python
class PrescriptionType(models.TextChoices):
    SYSTEMIC = "systemic"     # via oral, injetável
    TOPICAL = "topical"       # creme, loção, pomada
    DIETARY = "dietary"       # plano alimentar
    PHYSIOTHERAPY = "physio"  # exercícios
```

Template tópico inclui:

* nome do princípio ativo
* concentração
* veículo (creme, loção, gel)
* região de aplicação
* frequência
* duração
* observações (proteção solar obrigatória etc.)

---

# 9. PROTOCOLO DE SEGUIMENTO

Lesões com `suspicion="moderate"` ou `"high"` geram automaticamente:

* `Reminder` na agenda
* automação WhatsApp 6 meses depois
* alerta visual no prontuário do paciente
* dashboard de "lesões em seguimento"

---

# 10. INTEGRAÇÃO COM TEMPLATES

Templates seed:

* `anamnese_dermato` — antecedentes pessoais e familiares, exposição solar, etc.
* `descricao_lesao` — inclui `body_map` field
* `dermatoscopia` — foto + observações
* `prescricao_topica` — receita tópica
* `pos_procedimento_dermato` — orientações pós-procedimento

Ver `docs/templates-clinicos.md`.

---

# 11. INTEGRAÇÃO COM AGENDA

* tipos de consulta: avaliação, retorno, procedimento, biópsia, seguimento de rastreio
* duração padrão por tipo
* alerta no agendamento se paciente tem lesão suspeita pendente

---

# 12. INTEGRAÇÃO COM PACOTES

Pacotes comuns:

* "Protocolo Laser 6 sessões"
* "Tratamento de Acne 4 sessões"
* "Microagulhamento 3 sessões"

Ver `docs/pacotes-tratamento.md`.

---

# 13. WHATSAPP / COMUNICAÇÃO

Templates específicos:

* `dermato_pos_procedimento` — orientações pós-procedimento (limpeza, protetor solar, retorno)
* `dermato_resultado_biopsia` — aviso de resultado disponível (sem expor diagnóstico)
* `dermato_seguimento` — lembrete de rastreio periódico
* `dermato_orientacao_solar` — orientação de fotoproteção

Ver `docs/whatsapp.md`.

---

# 14. IA (FASE 3)

Casos de uso planejados:

* análise de imagem dermatoscópica (sugestão ABCDE)
* sugestão de CID com base em descrição
* sumarização do histórico de lesões
* alertas de mudanças significativas

Sempre como **assistente**, nunca como diagnóstico.

---

# 15. PORTAL DO PACIENTE

Paciente vê:

* fotos clínicas autorizadas
* receitas tópicas e sistêmicas
* lembretes de seguimento
* orientações pós-procedimento
* parcelas de pacote (se aplicável)

---

# 16. KPIs

* nº de novas lesões cadastradas
* nº de lesões em seguimento
* taxa de biópsia confirmando suspeita
* receita por procedimento
* taxa de retorno de seguimento (% pacientes que retornaram conforme agendado)

---

# 17. EXPORTAÇÃO

* relatório de lesões em PDF (foto + descrição + ABCDE)
* histórico de prescrições tópicas/sistêmicas
* exportação completa do prontuário dermato

---

# 18. AUDITORIA

* `lesion.added`
* `lesion.updated`
* `dermatoscopy.recorded`
* `biopsy.requested`
* `biopsy.result_received`
* `topical_prescription.issued`

---

# 19. EXEMPLOS DE FLUXOS

## Rastreio de rotina

* paciente vem para rastreio anual
* profissional revisa todas as lesões
* atualiza body map
* registra dermatoscopia das suspeitas
* agenda retorno em 6 meses ou indica biópsia

## Biópsia

* lesão marcada como `suspicion="high"`
* biopsy order criada
* paciente assina termo
* amostra enviada ao lab parceiro
* resultado importado quando disponível
* WhatsApp avisa paciente (sem expor diagnóstico)
* consulta de retorno para discussão

## Tratamento com laser

* avaliação inicial define protocolo
* venda do pacote "Laser 6 sessões"
* sessões executadas com fotos antes/depois
* automação envia orientações pós-procedimento
* dashboard mostra progresso

---

# 20. O QUE EVITAR

* salvar lesão sem coordenada corporal
* registrar foto clínica sem consentimento
* perder histórico de versões do body map
* enviar resultado de biópsia por canal não-seguro
* esquecer follow-up de lesão suspeita

---

# 21. OBJETIVO FINAL

Módulo dermatológico:

* moderno e visual
* preciso no rastreamento
* integrado com imagem clínica, agenda, financeiro, pacotes
* alimentado por IA no futuro
* seguro juridicamente (CFM + LGPD)

---

# 22. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/imagens-clinicas.md`
* `docs/pacotes-tratamento.md`
* `docs/whatsapp.md`
* `docs/ia.md`
* `docs/portal-paciente.md`
* `docs/compliance-conselhos.md` (CRM/CFM)
