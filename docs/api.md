# API — Clinix

> Padrões, convenções e diretrizes da API REST do Clinix.

---

# 1. VISÃO GERAL

A API do Clinix é a fundação do produto.

Todo o sistema é API-first:

* web (Next.js)
* mobile (React Native)
* integrações
* portal do paciente
* webhooks
* automações

Tudo consome a mesma API.

---

# 2. OBJETIVOS

Garantir:

* consistência
* previsibilidade
* segurança
* performance
* documentação
* versionamento
* facilidade de integração

---

# 3. PRINCÍPIOS

A API deve priorizar:

* REST
* JSON
* tipagem clara
* respostas previsíveis
* erros consistentes
* paginação obrigatória
* filtros padronizados

---

# 4. PADRÃO REST

Toda API deve seguir:

```txt
GET    /api/v1/patients/        → list
POST   /api/v1/patients/        → create
GET    /api/v1/patients/:id/    → retrieve
PATCH  /api/v1/patients/:id/    → partial update
PUT    /api/v1/patients/:id/    → full update
DELETE /api/v1/patients/:id/    → soft delete
```

---

# 5. VERSIONAMENTO

Padrão oficial:

```txt
/api/v1/
```

---

# Regras

* nunca quebrar versões publicadas
* breaking changes exigem v2
* deprecation prévia com headers
* documentação sempre atualizada

---

# 6. AUTENTICAÇÃO

Toda API exige:

* JWT válido
* tenant resolvido
* RBAC validado

---

# Header obrigatório

```txt
Authorization: Bearer <access_token>
```

---

# 7. TENANT

Todo endpoint deve resolver tenant via JWT.

---

# Nunca permitir

* `tenant_id` no payload
* `tenant_id` em query params
* override de tenant pelo cliente

---

# 8. RESPOSTAS PADRÃO

## Sucesso simples

```json
{
  "id": 1,
  "name": "Maria Silva"
}
```

---

## Listagem paginada

```json
{
  "count": 142,
  "next": "/api/v1/patients/?page=2",
  "previous": null,
  "results": [
    { "id": 1, "name": "Maria Silva" }
  ]
}
```

---

# 9. ERROS PADRÃO

Formato único para todos os erros:

```json
{
  "error": {
    "code": "validation_error",
    "message": "Mensagem amigável em PT-BR",
    "details": {
      "cpf": ["CPF inválido"]
    }
  }
}
```

---

# 10. STATUS HTTP

Padrão a seguir:

* 200 → sucesso
* 201 → criado
* 204 → sem conteúdo
* 400 → erro de validação
* 401 → não autenticado
* 403 → sem permissão
* 404 → não encontrado
* 409 → conflito
* 422 → entidade inprocessável
* 429 → rate limit
* 500 → erro interno

---

# 11. PAGINAÇÃO

Padrão obrigatório em todas as listagens.

---

## Query params

```txt
?page=1
?page_size=20
```

---

## Limites

* page_size padrão: 20
* page_size máximo: 100

---

# 12. FILTROS

Filtros via query params.

---

## Exemplos

```txt
?status=active
?professional_id=10
?date_from=2026-01-01
?date_to=2026-01-31
?search=maria
```

---

# 13. ORDENAÇÃO

Padrão via `ordering`:

```txt
?ordering=-created_at
?ordering=name,birth_date
```

---

# 14. BUSCA

Toda listagem com busca textual deve aceitar:

```txt
?search=<termo>
```

Com suporte a:

* busca fuzzy (`pg_trgm`)
* full-text search em português
* normalização de acentos

---

# 15. CAMPOS SELECIONÁVEIS

Futuramente suportar:

```txt
?fields=id,name,email
?expand=professional,appointments
```

---

# 16. NAMING

## Endpoints

* kebab-case nas URLs
* plural sempre: `/patients/`, `/appointments/`

---

## Payloads

* `snake_case` em todos os campos JSON

---

## Exemplo

```json
{
  "full_name": "Maria Silva",
  "birth_date": "1990-05-10",
  "created_at": "2026-05-27T10:00:00Z"
}
```

---

# 17. DATAS

Toda data deve ser:

* ISO 8601
* UTC
* timezone explícita

---

## Exemplo

```txt
2026-05-27T10:00:00-03:00
```

---

# 18. IDs

* IDs públicos via UUID em entidades sensíveis (pacientes, prontuários)
* IDs internos podem ser BigInt
* nunca expor sequência incremental em recursos críticos

---

# 19. RATE LIMITING

Toda API pública deve possuir rate limit.

---

## Estratégia inicial

* 1000 req/min por tenant
* 100 req/min por IP em endpoints de auth
* 60 req/min em endpoints de WhatsApp

---

# 20. CACHE

Endpoints de leitura pesada devem possuir:

* ETag
* `Cache-Control` apropriado
* invalidação por evento

---

# 21. WEBHOOKS

O sistema deve suportar webhooks de saída.

---

## Eventos

* `appointment.created`
* `appointment.cancelled`
* `payment.confirmed`
* `exam.released`
* `patient.created`

---

## Segurança

* assinatura HMAC
* retry com backoff exponencial
* dead-letter queue
* logs de entrega

---

# 22. DOCUMENTAÇÃO

A API deve possuir:

* OpenAPI 3.x
* Swagger UI
* Redoc
* exemplos de payload

---

## Tooling

* `drf-spectacular`

---

# 23. PADRÕES DE NEGÓCIO

Operações de negócio complexas devem ter endpoints dedicados:

```txt
POST /api/v1/appointments/:id/confirm/
POST /api/v1/appointments/:id/check-in/
POST /api/v1/appointments/:id/cancel/
POST /api/v1/exams/:id/release/
POST /api/v1/patients/:id/anonymize/
```

---

# 24. BATCH OPERATIONS

Endpoints de bulk devem ser explícitos:

```txt
POST /api/v1/appointments/bulk-cancel/
POST /api/v1/whatsapp/messages/bulk-send/
```

---

# 25. UPLOADS

Toda API de upload deve:

* aceitar multipart/form-data
* validar mime-type
* validar tamanho
* gerar URL assinada
* armazenar no R2/S3
* nunca persistir no container

---

# 26. DOWNLOADS

Toda API de download de arquivo sensível deve:

* exigir autenticação
* gerar URL assinada com TTL
* registrar acesso em audit log
* respeitar permissões

---

# 27. CONSULTAS GRANDES

Endpoints de export (CSV, PDF) devem:

* ser assíncronos
* retornar `job_id`
* notificar conclusão via webhook ou polling
* nunca bloquear a request

---

# 28. RESPONSE TIME

Metas operacionais:

* 95% das requests < 300ms
* 99% das requests < 1s
* endpoints assíncronos retornam imediatamente

---

# 29. PERMISSÕES

Todo endpoint deve declarar permissões:

```python
permission_classes = [
    IsAuthenticated,
    HasTenantContext,
    HasModulePermission("patients", "view"),
]
```

---

# 30. AUDITORIA

Endpoints críticos devem gerar audit log:

* alterações de prontuário
* alterações financeiras
* alterações de permissões
* exclusões

---

# 31. IDEMPOTÊNCIA

Endpoints `POST` críticos devem aceitar:

```txt
Idempotency-Key: <uuid>
```

Especialmente em:

* pagamentos
* envio de WhatsApp
* criação de cobranças

---

# 32. SOFT DELETE

`DELETE` em entidades críticas executa soft delete.

Para hard delete usar endpoint dedicado:

```txt
POST /api/v1/patients/:id/permanent-delete/
```

Restrito a admin + auditado.

---

# 33. HEALTHCHECK

A API deve possuir:

```txt
GET /api/healthz   → liveness
GET /api/readyz    → readiness (DB, Redis, Celery)
```

---

# 34. INTERNACIONALIZAÇÃO

Toda API deve aceitar:

```txt
Accept-Language: pt-BR
```

Mensagens de erro traduzíveis.

---

# 35. EVOLUÇÃO

Futuramente avaliar:

* GraphQL para BI
* gRPC interno entre serviços
* server-sent events para realtime leve

---

# 36. O QUE EVITAR

Nunca permitir:

* endpoints sem autenticação indevidos
* respostas sem padrão
* exposição de stack trace em produção
* erros sem código padronizado
* endpoints sem versionamento
* listagens sem paginação
* queries sem tenant

---

# 37. NAMESPACES POR ESPECIALIDADE

A maior parte do domínio é genérica (pacientes, agendamentos, prontuário composable). Quando uma especialidade precisa de endpoints específicos, ficam em sub-namespace dedicado.

## Padrão

```
/api/v1/                                 — recursos genéricos
/api/v1/specialty/{area}/...             — recursos específicos por especialidade
```

## Exemplos

### Dermatologia

```
GET    /api/v1/specialty/dermatology/lesion-maps/{patient_id}/
POST   /api/v1/specialty/dermatology/lesion-maps/{patient_id}/
GET    /api/v1/specialty/dermatology/lesions/?patient={id}&needs_followup=true
POST   /api/v1/specialty/dermatology/dermatoscopy/
POST   /api/v1/specialty/dermatology/biopsies/
PATCH  /api/v1/specialty/dermatology/biopsies/{id}/result/
```

### Estética

```
GET    /api/v1/specialty/aesthetics/procedures/                  # catálogo
POST   /api/v1/specialty/aesthetics/assessments/
POST   /api/v1/specialty/aesthetics/contraindication-checks/
```

### Nutrição

```
POST   /api/v1/specialty/nutrition/assessments/
POST   /api/v1/specialty/nutrition/plans/                        # cria nova versão
GET    /api/v1/specialty/nutrition/plans/{patient_id}/current/
POST   /api/v1/specialty/nutrition/plans/{id}/send/              # envia ao paciente
GET    /api/v1/foods/?q=...                                       # catálogo global
GET    /api/v1/specialty/nutrition/anthropometry/?patient={id}
```

### Odontologia

```
GET    /api/v1/specialty/dental/charts/{patient_id}/
POST   /api/v1/specialty/dental/charts/{patient_id}/version/
POST   /api/v1/specialty/dental/procedures/
GET    /api/v1/specialty/dental/treatment-plans/{patient_id}/
```

### Psicologia

```
GET    /api/v1/scales/                                            # catálogo
POST   /api/v1/scales/{code}/applications/
GET    /api/v1/scales/{code}/applications/?patient={id}
```

### Componentes transversais

```
POST   /api/v1/uploads/clinical-image/sign                        # presigned PUT
POST   /api/v1/clinical-images/{id}/finalize
GET    /api/v1/clinical-images/?patient={id}&category=before

GET    /api/v1/treatment-packages/                                # catálogo
POST   /api/v1/treatment-package-instances/                       # venda
POST   /api/v1/treatment-package-sessions/                        # executar

GET    /api/v1/anthropometry/?patient={id}
POST   /api/v1/anthropometry/

GET    /api/v1/record-templates/?specialty=dermatology
POST   /api/v1/record-entries/
```

## Princípios

* **versionamento global** — todas as rotas sob `/api/v1/`
* **especialidade não vaza para recursos genéricos** — pacientes e agendamentos são únicos
* **tenant inferido do JWT** — nunca passar `tenant_id` em query string
* **paginação, filtros, ordenação** — padrão consistente em todas as rotas
* **validação dinâmica** — `record-entries` valida `content_json` contra `record-template.schema_json`
* **permissões por especialidade** — ver `docs/auth.md`

Detalhes em:
* `docs/dermatologia.md`
* `docs/estetica.md`
* `docs/nutricao.md`
* `docs/odontograma.md`
* `docs/escalas-testes.md`
* `docs/templates-clinicos.md`
* `docs/pacotes-tratamento.md`
* `docs/antropometria.md`
* `docs/imagens-clinicas.md`

---

# 38. OBJETIVO FINAL

Construir uma API:

* consistente
* segura
* documentada
* versionada
* performática
* preparada para web, mobile e integrações
* preparada para crescimento enterprise
* extensível por especialidade sem comprometer a generalidade do core
