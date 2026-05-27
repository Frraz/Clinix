# Clínicas de Estética — Clinix

> Módulo de estética do Clinix. Pacotes, fotos antes/depois, contraindicações, fluxo de tratamento.

---

# 1. VISÃO GERAL

Clínicas de estética têm operação muito diferente de uma clínica médica tradicional:

* pacotes de N sessões são a venda principal
* fotos antes/depois são prova de resultado (e marketing, com consentimento)
* contraindicações precisam ser checadas antes de cada procedimento
* protocolos por procedimento (limpeza de pele, drenagem, peeling, etc.)
* recorrência e fidelização são vitais para o negócio
* manutenção pós-tratamento gera receita recorrente

O Clinix atende clínicas de estética com prontuário composable + pacotes + imagens clínicas + automações.

---

# 2. OBJETIVOS

* venda fluida de pacote de tratamento
* execução fluida de sessão (check-in → procedimento → foto antes/depois → próximo agendamento)
* checklist de contraindicações antes de cada procedimento
* registro fotográfico padronizado e seguro
* renovação e upsell automatizado
* relatórios de receita por procedimento e profissional

---

# 3. PRINCÍPIOS

* **anamnese estética é obrigatória antes do primeiro procedimento**
* **toda sessão consome 1 do pacote** (sem exceções sem motivo registrado)
* **toda foto tem consentimento próprio**
* **contraindicações têm peso clínico-jurídico**
* **fidelização é first-class** — automações cuidam disso

---

# 4. FLUXO CLÍNICO

```txt
captação (lead ou paciente recorrente)
   ↓
agendamento de avaliação
   ↓
anamnese estética completa
   ↓
checklist de contraindicações
   ↓
orçamento (com pacotes sugeridos)
   ↓
aprovação do paciente (termo digital)
   ↓
venda do pacote + parcelamento
   ↓
agendamento das N sessões (opcionalmente em série)
   ↓
para cada sessão:
    check-in
    revalidação rápida de contraindicações
    foto antes
    execução do procedimento
    foto depois
    registro de evolução
    consumo de 1 sessão
    próximo agendamento
   ↓
conclusão do pacote
   ↓
upsell / manutenção
```

---

# 5. ENTIDADES

## EstheticAssessment

```python
class EstheticAssessment(TenantAwareModel):
    patient = models.ForeignKey(Patient, ...)
    professional = models.ForeignKey(User, ...)
    record_entry = models.ForeignKey(RecordEntry, ...)

    # Anamnese
    skin_type = models.CharField(max_length=30)  # I-VI Fitzpatrick
    photo_type = models.CharField(max_length=30, blank=True)
    main_complaint = models.TextField()
    expectations = models.TextField()

    # Contraindicações
    contraindications_json = models.JSONField()  # checklist completo

    # Hábitos e histórico
    sun_exposure = models.CharField(max_length=50)
    smoker = models.BooleanField(default=False)
    alcohol_frequency = models.CharField(max_length=50, blank=True)

    # Antropometria opcional (avaliação corporal)
    anthropometry = models.OneToOneField(
        AnthropometryRecord, null=True, ...
    )

    assessed_at = models.DateTimeField(auto_now_add=True)
```

---

## ContraindicationChecklist

Lista de contraindicações por tipo de procedimento (catálogo global por especialidade):

```python
class Contraindication(models.Model):
    code = models.SlugField(unique=True)  # "pregnancy", "isotretinoin", "pacemaker"
    label = models.CharField(max_length=200)
    description = models.TextField()
    severity = models.CharField(
        choices=[("absolute", ...), ("relative", ...)]
    )
    applies_to = models.JSONField()  # ["laser", "criofrequency", "peeling"]
```

Verificação antes de cada sessão:

```python
class ProcedureChecklistResponse(TenantAwareModel):
    package_session = models.ForeignKey(TreatmentPackageSession, ...)
    contraindication = models.ForeignKey(Contraindication, ...)
    answer = models.BooleanField()
    note = models.TextField(blank=True)
    answered_by = models.ForeignKey(User, ...)
```

---

## AestheticProcedure

```python
class AestheticProcedure(TenantAwareModel):
    name = models.CharField(max_length=200)
    code = models.SlugField()
    description = models.TextField()
    duration_minutes = models.PositiveIntegerField()
    requires_consent_term = models.BooleanField(default=True)
    photo_categories_required = models.JSONField()  # ["before", "after"]
    contraindications = models.ManyToManyField(Contraindication)
    suggested_interval_days = models.PositiveIntegerField(default=14)
    base_price = models.DecimalField(...)
```

Catálogo de procedimentos da clínica.

---

# 6. PACOTES

Reusa `TreatmentPackage` / `TreatmentPackageInstance` / `TreatmentPackageSession`. Ver `docs/pacotes-tratamento.md`.

Pacotes comuns em estética:

* Drenagem linfática (10 sessões)
* Limpeza de pele (mensal)
* Peeling químico (4 sessões)
* Microagulhamento facial (3 sessões)
* Criofrequência abdominal (10 sessões)
* Massagem modeladora (12 sessões)
* Radiofrequência facial (6 sessões)

---

# 7. FOTOS ANTES / DEPOIS

Padrão obrigatório por sessão (quando configurado para o procedimento):

* **antes**: capturada no check-in
* **depois**: capturada ao final
* **mesmo ângulo, mesma iluminação, mesma distância**
* `ClinicalImage.category = "before" | "after"`
* `paired_with` vincula as duas
* slider de comparação no portal e no prontuário

Detalhes em `docs/imagens-clinicas.md`.

---

# 8. TERMO DE CONSENTIMENTO

Antes do primeiro procedimento de um tipo:

* termo digital específico (não genérico)
* descreve o procedimento, riscos, contraindicações
* paciente assina digitalmente
* registra IP, timestamp, hash, versão do termo
* PDF gerado e arquivado no prontuário

Termos diferentes por procedimento (laser tem termo próprio, peeling outro, etc.).

---

# 9. INTEGRAÇÃO COM TEMPLATES

Templates seed:

* `anamnese_estetica` — completa
* `checklist_contraindicacoes` — por procedimento
* `sessao_estetica` — fotos antes/depois + observações
* `avaliacao_corporal` — antropometria + análise visual

Ver `docs/templates-clinicos.md`.

---

# 10. INTEGRAÇÃO COM AGENDA

* tipos de consulta: avaliação inicial, sessão de pacote, retorno, manutenção
* duração configurada por procedimento
* alerta no agendamento se contraindicação ativa
* opção de agendar todas as N sessões em série

---

# 11. INTEGRAÇÃO COM FINANCEIRO

* pacote parcelado (até 12x sem juros configurável)
* recorrência via Mercado Pago
* relatório de receita por procedimento
* comissão por profissional (Fase 2)

---

# 12. INTEGRAÇÃO COM CRM (FASE 2)

* lead qualificado vira paciente
* funil de captação
* recuperação de avaliações que não fecharam
* segmentação por procedimento (campanhas direcionadas)

---

# 13. WHATSAPP / COMUNICAÇÃO

Templates específicos:

* `estetica_confirmacao_sessao` — lembrete 24h antes
* `estetica_pos_sessao` — orientações pós-procedimento
* `estetica_lembrete_proxima` — próxima sessão do pacote (D-3)
* `estetica_manutencao` — sugestão de manutenção (mensal/trimestral)
* `estetica_aniversario_cliente` — fidelização

Ver `docs/whatsapp.md`.

---

# 14. PORTAL DO PACIENTE

Paciente vê:

* pacotes ativos com progresso
* próximas sessões
* fotos antes/depois (autorizadas)
* termos de consentimento assinados
* parcelas
* indicação de manutenção

---

# 15. KPIs

* receita por procedimento
* ticket médio por pacote
* taxa de conversão (avaliação → pacote)
* taxa de conclusão de pacote
* nº de pacotes ativos
* receita por profissional
* LTV por cliente
* taxa de renovação

---

# 16. RELATÓRIOS

* faturamento por procedimento
* faturamento por profissional
* pacotes em risco (atrasados em sessões)
* pacientes prontos para renovar

---

# 17. AUDITORIA

* `aesthetic_assessment.created`
* `contraindication.flagged`
* `procedure_consent.signed`
* `package.sold`
* `package_session.executed`
* `before_after_photo.captured`

---

# 18. MARKETING (OPT-IN)

Com consentimento específico, fotos antes/depois podem ser usadas em:

* portfólio público da clínica
* mídias sociais (com mosaico opcional)
* materiais comerciais

**Sem opt-in, nunca**. Termo separado do termo clínico.

---

# 19. AUTOMAÇÕES OPERACIONAIS

* recuperação de avaliação não-fechada (lead frio): WhatsApp D+3
* lembrete de manutenção mensal
* upsell baseado em histórico (paciente que fez X tem afinidade com Y)
* parabéns no aniversário com cupom

---

# 20. CONTRAINDICAÇÕES COMUNS

Catálogo seed:

* gestação / amamentação
* uso de isotretinoína nos últimos 6 meses
* marca-passo (para radiofrequência)
* epilepsia (para alguns aparelhos)
* hipersensibilidade ao princípio ativo
* lesões ativas na área
* histórico de queloide
* doenças autoimunes ativas
* uso de anticoagulantes (para procedimentos invasivos)

Ampliado por procedimento.

---

# 21. COMPLIANCE PROFISSIONAL

* a depender do procedimento, exige profissional habilitado (médico esteta, biomédico estético, esteticista com formação específica)
* o sistema valida que o profissional tem `specialty` permitida para executar
* registro do conselho fica visível em todo documento gerado

Ver `docs/compliance-conselhos.md`.

---

# 22. EXEMPLOS DE FLUXOS

## Venda de pacote

1. Paciente vem para avaliação
2. Profissional preenche anamnese estética + checklist
3. Sistema sugere protocolo
4. Orçamento gerado (1 pacote ou combo)
5. Paciente aprova e assina termo
6. Pagamento parcelado configurado
7. N sessões agendadas

## Execução de sessão

1. Check-in
2. Revalidação rápida de contraindicações (alguma mudança?)
3. Foto antes
4. Procedimento (registra evolução)
5. Foto depois
6. Sessão consumida no pacote
7. Próxima sessão agendada
8. WhatsApp com orientações pós

## Renovação

1. Última sessão executada
2. Sistema sugere renovação imediata ou manutenção
3. Profissional aprova
4. Novo pacote vendido ou agendamentos mensais criados

---

# 23. O QUE EVITAR

* executar sessão sem checklist
* salvar foto sem consentimento
* permitir profissional sem habilitação executar procedimento que exige
* vender pacote sem termo digital
* perder fotos antes/depois pareadas
* contar sessão extra além do pacote sem aviso financeiro

---

# 24. OBJETIVO FINAL

Módulo de estética:

* operacionalmente fluido
* visualmente premium
* seguro juridicamente
* foco em fidelização e LTV
* preparado para escalar a operação da clínica

---

# 25. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/pacotes-tratamento.md`
* `docs/imagens-clinicas.md`
* `docs/antropometria.md`
* `docs/whatsapp.md`
* `docs/billing.md`
* `docs/portal-paciente.md`
* `docs/compliance-conselhos.md`
* `examples/fluxo-pacote-tratamento.md`
