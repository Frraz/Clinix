# Tenancy — Clinix

> Estratégia de multi-tenancy, isolamento de dados e arquitetura multi-clínica do Clinix.

---

# 1. VISÃO GERAL

O Clinix será uma plataforma:

# Multi-Tenant

Isso significa que:

* múltiplas clínicas utilizarão o mesmo sistema
* os dados serão isolados logicamente
* cada clínica terá seu próprio ambiente operacional dentro da plataforma

---

# 2. OBJETIVOS DO MULTI-TENANCY

Garantir:

* isolamento de dados
* segurança
* escalabilidade
* baixo custo operacional
* facilidade de manutenção
* crescimento SaaS
* operação multi-clínica
* expansão futura

---

# 3. ESTRATÉGIA ESCOLHIDA

## Estratégia oficial

# Isolamento lógico por tenant_id

---

# Motivos

* simplicidade operacional
* menor custo
* facilidade de manutenção
* melhor produtividade
* evolução mais rápida
* arquitetura SaaS moderna
* excelente custo-benefício

---

# 4. COMO FUNCIONARÁ

Toda entidade operacional possuirá:

```sql
tenant_id
```

---

# Exemplo

```sql
patients
appointments
financial
medical_records
whatsapp_messages
```

Todas possuirão:

```sql
tenant_id
```

---

# 5. REGRA CRÍTICA

Nenhuma informação pode ser compartilhada entre tenants.

---

# Isso inclui

* pacientes
* agenda
* financeiro
* prontuários
* documentos
* mensagens
* relatórios
* dashboards
* métricas
* arquivos

---

# 6. FALHAS CRÍTICAS

São consideradas falhas gravíssimas:

* vazamento de dados entre clínicas
* queries sem tenant
* APIs sem isolamento
* permissões sem contexto
* cache compartilhado incorretamente

---

# 7. CONTEXTO DO TENANT

Toda requisição deve possuir contexto do tenant.

---

# O tenant poderá ser identificado por:

* subdomínio futuramente
* usuário autenticado
* contexto da sessão
* token JWT

---

# 8. JWT E TENANT

O JWT deve possuir:

```json
{
  "user_id": 1,
  "tenant_id": 10,
  "roles": []
}
```

---

# 9. REGRAS DE QUERY

Toda query obrigatoriamente deve considerar tenant.

---

# Estratégia de proteção

O sistema deve possuir:

* `TenantAwareManager` como manager padrão
* filtro automático por `tenant_id` na origem
* assertion em desenvolvimento que bloqueia queries sem contexto
* code review obrigatório em queries cross-tenant

---

# Exemplo correto

```python
Patient.objects.for_tenant(request.tenant)
```

---

# Nunca permitir

```python
Patient.objects.all()
```

Sem isolamento.

---

# Camada extra de segurança

Tabelas críticas devem possuir:

* Row-Level Security do PostgreSQL
* policies por `tenant_id`
* proteção em camada de banco

Aplicar em:

* medical_records
* prescriptions
* exams
* laboratory_results
* payments

---

# 10. MIDDLEWARE DE TENANT

O backend deve possuir middleware responsável por:

* resolver tenant
* validar tenant
* injetar contexto
* bloquear acessos inválidos

---

# 11. APIs

Toda API deve:

* validar tenant
* validar permissões
* impedir acesso cruzado
* garantir isolamento

---

# 12. FRONTEND E TENANT

O frontend deve sempre operar em contexto de tenant.

---

# Toda interface deve considerar

* tenant atual
* branding futuro
* permissões contextualizadas
* dados isolados

---

# Nunca permitir

* vazamento visual
* troca incorreta de contexto
* cache misturado

---

# 13. CACHE E TENANT

Todo cache deve considerar tenant.

---

# Exemplo correto

```txt
tenant:10:patients:list
```

---

# Nunca permitir

```txt
patients:list
```

Sem contexto.

---

# 14. STORAGE E TENANT

Arquivos devem possuir isolamento por tenant.

---

# Estrutura sugerida

```txt
/tenant-10/patients/
/tenant-10/exams/
/tenant-10/documents/
```

---

# 15. AUDITORIA

Toda auditoria deve registrar:

* tenant
* usuário
* ação
* entidade
* timestamp

---

# 16. PERMISSÕES

RBAC deve ser contextualizado por tenant.

---

# Exemplo

Um usuário pode:

* ser admin em uma clínica
* e recepcionista em outra

---

# 17. MULTIUNIDADE

O sistema deve suportar:

# tenant -> múltiplas unidades

---

# Exemplo

```txt
Tenant
 ├── Unidade Centro
 ├── Unidade Hospital
 └── Unidade Laboratório
```

---

# 18. ESTRUTURA HIERÁRQUICA

## Hierarquia principal

```txt
Tenant
 ├── Units
 │    ├── Professionals
 │    ├── Appointments
 │    ├── Rooms
 │    └── Financial
```

---

# 19. BANCO DE DADOS

## Estratégia atual

Banco único compartilhado.

---

# Estrutura

```txt
1 banco PostgreSQL
N tenants
```

---

# Motivos

* simplicidade
* velocidade de desenvolvimento
* custo reduzido
* facilidade operacional

---

# 20. EVOLUÇÃO FUTURA

A arquitetura deve permitir futuramente:

* schema por tenant
* database por tenant
* isolamento híbrido
* tenants enterprise dedicados

Sem reescrever o sistema.

---

# 21. TENANT SETTINGS

Cada tenant poderá possuir:

* configurações próprias
* branding
* automações
* integrações
* permissões
* templates
* workflows

---

# 22. CUSTOMIZAÇÕES FUTURAS

Cada clínica poderá personalizar:

* logo
* cores
* mensagens
* templates
* automações
* permissões
* configurações operacionais

---

# 23. BILLING MULTI-TENANT

O sistema deve suportar:

* assinatura por tenant
* planos
* limites
* cobrança recorrente
* recursos por plano

---

# 24. LIMITES POR TENANT

Futuramente o sistema poderá limitar:

* usuários
* armazenamento
* mensagens
* unidades
* automações
* integrações

---

# 25. OBSERVABILIDADE

Logs e métricas devem considerar tenant.

---

# Exemplos

* uso por clínica
* consumo de storage
* uso de WhatsApp
* métricas operacionais
* métricas financeiras

---

# 26. IA E TENANT

Toda IA deve respeitar:

* isolamento
* permissões
* contexto da clínica
* LGPD

---

# Nunca permitir

* treinamento cruzado indevido
* exposição de dados sensíveis

---

# 27. SEGURANÇA

Toda arquitetura multi-tenant deve considerar:

* LGPD
* RBAC
* auditoria
* rastreabilidade
* proteção de dados
* isolamento rigoroso

---

# 28. TESTES

O sistema deve possuir testes específicos para tenancy.

---

# Testes obrigatórios

* isolamento entre tenants
* permissões
* APIs
* cache
* auditoria
* uploads

---

# 29. BACKEND RULES

Toda entidade operacional deve:

* possuir tenant_id
* validar tenant
* respeitar RBAC
* gerar auditoria quando necessário

---

# 30. FRONTEND RULES

Toda interface deve:

* respeitar tenant atual
* ocultar dados indevidos
* respeitar permissões
* evitar inconsistências visuais

---

# 31. EVENTOS E TENANT

Todo evento deve carregar:

```json
{
  "tenant_id": 10
}
```

---

# 32. FILAS E TENANT

Toda fila deve preservar contexto do tenant.

---

# Exemplo

```python
send_whatsapp.delay(
    tenant_id=10,
    patient_id=55
)
```

---

# 33. WEBHOOKS E TENANT

Toda integração deve identificar tenant corretamente.

---

# Nunca permitir

* callbacks sem tenant
* webhooks ambíguos
* integrações sem contexto

---

# 34. RELATÓRIOS

Todo relatório deve:

* respeitar tenant
* respeitar permissões
* respeitar unidade quando necessário

---

# 35. BACKUPS

Backups devem considerar:

* segurança
* isolamento
* recuperação seletiva futura

---

# 36. PERFORMANCE

Toda estratégia multi-tenant deve considerar:

* índices por tenant
* cache contextualizado
* paginação
* queries eficientes

---

# Índices importantes

```sql
tenant_id
status
created_at
```

---

# 37. ESCALABILIDADE

A estratégia escolhida deve permitir:

* milhares de clínicas
* milhões de registros
* crescimento gradual
* expansão enterprise

---

# 38. O QUE EVITAR

Nunca permitir:

* bypass de tenant
* lógica sem isolamento
* queries globais
* cache sem contexto
* permissões genéricas
* uploads misturados

---

# 39. FILOSOFIA MULTI-TENANT

O multi-tenancy do Clinix deve transmitir:

* segurança
* previsibilidade
* robustez
* organização
* escalabilidade

---

# 40. OBJETIVO FINAL

Construir uma arquitetura multi-tenant:

* sólida
* segura
* escalável
* preparada para SaaS
* preparada para crescimento
* preparada para operação enterprise

Sem perder:

* simplicidade
* produtividade
* performance
* baixo custo operacional inicial
