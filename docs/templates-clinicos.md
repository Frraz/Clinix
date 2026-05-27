# Templates Clínicos — Clinix

> Engine de templates dinâmicos do prontuário Clinix. Serve as 8 especialidades-alvo com a mesma base.

---

# 1. VISÃO GERAL

Cada especialidade tem necessidades de prontuário diferentes:

* clínica médica usa SOAP
* odontologia precisa de odontograma
* fisioterapia evolui por sessão
* psicologia aplica escalas
* dermatologia mapeia lesões
* estética rastreia pacotes
* nutrição calcula antropometria
* biomedicina solicita exames

Construir um prontuário hard-coded por especialidade levaria a duplicação massiva. A solução é uma **engine única de templates** que cada especialidade consome.

---

# 2. OBJETIVOS

* uma engine que serve todas as 8 especialidades
* permitir personalização por clínica sem duplicar código
* validação de campos no backend e frontend
* versionamento de templates
* atualizar template sem invalidar dados antigos
* facilitar a criação de novos templates no futuro

---

# 3. PRINCÍPIOS

* **JSONB validado** — schema declarativo, não runtime
* **versionamento obrigatório** — atualizar template gera nova versão
* **dados antigos imutáveis** — uma entrada renderiza com a versão em que foi salva
* **sem JS arbitrário do tenant** — só o schema declarado
* **tipos compartilhados** — Zod no frontend espelha JSON Schema no backend

---

# 4. ENTIDADES

## Specialty

Catálogo das 8 especialidades-alvo:

```python
class Specialty(models.Model):
    code = models.CharField(max_length=50, unique=True)  # medical, dental, etc
    name = models.CharField(max_length=100)
    icon = models.CharField(max_length=50)
    is_active = models.BooleanField(default=True)
```

Códigos canônicos:

* `medical`
* `dental`
* `physiotherapy`
* `psychology`
* `biomedicine`
* `dermatology`
* `aesthetics`
* `nutrition`

---

## RecordTemplate

Template versionado vinculado a uma especialidade:

```python
class RecordTemplate(TenantAwareModel):
    specialty = models.ForeignKey(Specialty, ...)
    name = models.CharField(max_length=200)
    code = models.SlugField()  # ex: "anamnese_dermato"
    version = models.PositiveIntegerField(default=1)
    schema_json = models.JSONField()  # estrutura dos campos
    ui_json = models.JSONField()  # layout/agrupamento
    is_global = models.BooleanField(default=False)  # se False, é da clínica
    is_active = models.BooleanField(default=True)
    parent = models.ForeignKey('self', null=True)  # se herda de global
```

* templates `is_global=True` são distribuídos pelo Clinix (read-only para o tenant)
* clínica pode criar template próprio ou estender um global
* `version` incrementa a cada publicação

---

## RecordEntry

Entrada de prontuário usa um template específico:

```python
class RecordEntry(TenantAwareModel):
    medical_record = models.ForeignKey(MedicalRecord, ...)
    template = models.ForeignKey(RecordTemplate, ...)
    template_version = models.PositiveIntegerField()  # snapshot da versão
    content_json = models.JSONField()  # dados preenchidos
    is_private = models.BooleanField(default=False)  # psicologia
    created_by = models.ForeignKey(User, ...)
    created_at = models.DateTimeField(auto_now_add=True)
```

---

# 5. ESTRUTURA DO SCHEMA

`schema_json` segue uma especificação simplificada inspirada em JSON Schema + UI hints:

```json
{
  "fields": [
    {
      "key": "queixa_principal",
      "label": "Queixa principal",
      "type": "text",
      "required": true,
      "max_length": 500
    },
    {
      "key": "intensidade_dor",
      "label": "Intensidade da dor",
      "type": "scale",
      "min": 0,
      "max": 10,
      "step": 1
    },
    {
      "key": "tipo_lesao",
      "label": "Tipo de lesão",
      "type": "select",
      "options": ["nevo", "mancha", "verruga", "outra"],
      "required": true
    },
    {
      "key": "foto_lesao",
      "label": "Foto da lesão",
      "type": "image",
      "multiple": true,
      "max": 10
    }
  ]
}
```

---

# 6. TIPOS DE CAMPO SUPORTADOS

* `text` — texto curto
* `textarea` — texto longo
* `number` — número
* `decimal` — número com casas decimais
* `boolean` — sim/não
* `date` — data
* `datetime` — data e hora
* `select` — seleção única
* `multi_select` — múltipla
* `scale` — escala numérica (slider)
* `image` — upload de imagem
* `file` — upload de arquivo
* `signature` — assinatura visual
* `body_map` — mapa corporal (dermatologia)
* `dental_chart` — odontograma (odontologia)
* `anthropometry` — bloco antropométrico (nutrição)
* `meal_plan` — plano alimentar (nutrição)
* `scale_form` — escala validada (psicologia: PHQ-9 etc.)
* `subform` — subconjunto reutilizável

Cada tipo tem renderer no frontend e validador no backend.

---

# 7. UI JSON

`ui_json` define agrupamento e layout:

```json
{
  "groups": [
    {
      "title": "Anamnese",
      "fields": ["queixa_principal", "historia"]
    },
    {
      "title": "Exame físico",
      "fields": ["intensidade_dor", "foto_lesao"]
    }
  ]
}
```

---

# 8. VERSIONAMENTO

Regras:

* publicar um template incrementa `version`
* `RecordEntry` salva `template_version` que usou
* alterações em template global não afetam entradas antigas
* a clínica pode "fixar" uma versão para todo novo `RecordEntry` ou usar sempre a mais recente

---

# 9. HERANÇA

Clínica pode estender um template global:

```python
RecordTemplate(
    name="Anamnese Dermato (Clínica Bellare)",
    parent=template_global_anamnese_dermato,
    schema_json=template_global_schema + clinic_overrides,
    is_global=False
)
```

* novos campos adicionados não conflitam com o global
* campos do parent podem ser ocultados, não removidos
* update do parent não atinge filhos automaticamente — clínica precisa rebase manual

---

# 10. VALIDAÇÃO

## Backend

Serializer DRF valida `content_json` contra `schema_json`:

```python
class RecordEntrySerializer(serializers.ModelSerializer):
    def validate(self, data):
        template = data["template"]
        content = data["content_json"]
        errors = validate_against_schema(template.schema_json, content)
        if errors:
            raise serializers.ValidationError(errors)
        return data
```

---

## Frontend

Zod schema gerado dinamicamente a partir de `schema_json`:

```typescript
const zodSchema = buildZodSchema(template.schema_json);
const form = useForm({ resolver: zodResolver(zodSchema) });
```

* validação inline antes do submit
* mensagens de erro localizadas

---

# 11. PERMISSÕES

* templates globais: gerenciados apenas por admin Clinix
* templates da clínica: gerenciados pelos admins do tenant
* uso de templates: qualquer profissional com permissão de prontuário
* templates de psicologia respeitam `is_private` na entrada gerada

---

# 12. INDEXAÇÃO

PostgreSQL GIN index em `content_json` para busca rápida:

```sql
CREATE INDEX idx_record_content_gin ON record_entries USING GIN (content_json);
```

Permite queries como "todas as entradas com CID = J45" sem migração de schema.

---

# 13. EXPORTAÇÃO E IMPORTAÇÃO

* templates podem ser exportados em JSON
* clínica pode importar template criado por outra (se permitido)
* import valida estrutura antes de salvar
* template marcado com `source_hash` para auditoria

---

# 14. CICLO DE VIDA

```txt
draft → review → published → deprecated
```

* `draft` — apenas autor vê
* `review` — admin valida
* `published` — disponível para uso
* `deprecated` — não pode ser usado em entradas novas, mas entradas antigas continuam renderizando

---

# 15. EXEMPLOS DE TEMPLATES SEED

## Medicina geral

* `anamnese_medica_geral` — anamnese padrão
* `evolucao_soap` — SOAP estruturado

## Odontologia

* `anamnese_odontologica` — anamnese odonto
* `avaliacao_inicial_odonto` — inclui campo `dental_chart`

## Fisioterapia

* `avaliacao_funcional` — ROM, força, dor
* `evolucao_sessao_fisio` — campos por sessão

## Psicologia

* `anamnese_psicologica` — entrevista inicial
* `escala_phq9` — campo `scale_form` apontando para PHQ-9
* `evolucao_psicologica` — campos com `is_private=True`

## Biomedicina

* `solicitacao_exame` — campo de exames e urgência

## Dermatologia

* `anamnese_dermato` — anamnese
* `descricao_lesao` — inclui `body_map` e `image`

## Estética

* `anamnese_estetica` — anamnese + contraindicações
* `sessao_estetica` — fotos antes/depois + observações

## Nutrição

* `anamnese_nutricional` — anamnese + recordatório
* `avaliacao_antropometrica` — campo `anthropometry`
* `plano_alimentar` — campo `meal_plan`

---

# 16. EVOLUÇÃO FUTURA

* templates condicionais (campos aparecem baseados em respostas anteriores)
* templates colaborativos (vários profissionais preenchem partes)
* templates de IA-assistida (sugestão de preenchimento)
* marketplace de templates entre clínicas (com curadoria)

---

# 17. O QUE EVITAR

* permitir JS/HTML arbitrário no template
* alterar `schema_json` de um template publicado sem versionamento
* renderizar entrada antiga com schema novo
* expor templates de uma clínica a outra sem permissão
* tipos de campo proprietários sem renderer correspondente

---

# 18. REFERÊNCIAS

* `docs/prontuario.md`
* `docs/database-schema.md`
* `docs/api.md`
* `docs/personalizacao.md`
* `docs/dermatologia.md`
* `docs/estetica.md`
* `docs/nutricao.md`
* `docs/odontograma.md`
* `docs/escalas-testes.md`
* `docs/antropometria.md`
