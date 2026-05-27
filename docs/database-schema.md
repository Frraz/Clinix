# Database Schema — Clinix

> Estrutura conceitual inicial do banco de dados do Clinix.

---

# 1. VISÃO GERAL

O banco de dados do Clinix deve ser:

* escalável
* relacional
* performático
* auditável
* multi-tenant
* seguro
* preparado para crescimento

---

# 2. BANCO OFICIAL

# PostgreSQL

---

# Motivos

* robustez
* confiabilidade
* performance
* suporte relacional avançado
* JSONB
* escalabilidade
* excelente ecossistema

---

# 3. PRINCÍPIOS DE MODELAGEM

Toda modelagem deve priorizar:

* clareza
* integridade
* rastreabilidade
* performance
* baixo acoplamento
* escalabilidade futura

---

# 4. MULTI-TENANCY

## Regra obrigatória

Toda entidade operacional deve possuir:

```sql
tenant_id
```

---

# Objetivo

Garantir:

* isolamento
* segurança
* separação de dados
* controle multi-clínica

---

# Nunca permitir

* entidades sem tenant
* queries sem tenant
* vazamento entre clínicas

---

# 5. CAMPOS PADRÃO

Entidades principais devem possuir:

```sql
id
tenant_id
created_at
updated_at
created_by
updated_by
```

---

# 6. AUDITORIA

Entidades críticas devem permitir:

* rastreamento
* histórico
* logs
* alterações auditáveis

---

# 7. SOFT DELETE

Entidades críticas devem possuir:

```sql
deleted_at
deleted_by
```

---

# Objetivo

* preservar histórico
* evitar perda crítica de dados
* manter rastreabilidade

---

# 8. MÓDULOS DO BANCO

O banco será dividido logicamente em módulos.

---

# 9. CORE

## Responsável por

* tenancy
* usuários
* permissões
* autenticação
* auditoria
* configurações

---

# 10. TABELA: tenants

## Objetivo

Representar clínicas/empresas.

---

## Campos

```sql
id
name
slug
document
email
phone
status
created_at
updated_at
```

---

# 11. TABELA: users

## Objetivo

Usuários do sistema.

---

## Campos

```sql
id
tenant_id
name
email
password_hash
phone
avatar
is_active
last_login
created_at
updated_at
```

---

# 12. TABELA: roles

## Objetivo

Perfis e funções.

---

## Campos

```sql
id
tenant_id
name
slug
description
created_at
```

---

# 13. TABELA: permissions

## Objetivo

Permissões granulares.

---

## Campos

```sql
id
module
action
slug
created_at
```

---

# 14. TABELA: role_permissions

## Objetivo

Relacionamento RBAC.

---

## Campos

```sql
id
role_id
permission_id
```

---

# 15. TABELA: user_roles

## Objetivo

Relacionar usuários e funções.

---

## Campos

```sql
id
user_id
role_id
```

---

# 16. PACIENTES

## Objetivo

Centralizar dados de pacientes.

---

# 17. TABELA: patients

## Campos

```sql
id
tenant_id
full_name
cpf
rg
birth_date
gender
email
phone
secondary_phone
occupation
marital_status
blood_type
notes
status
created_at
updated_at
deleted_at
```

---

# 18. TABELA: patient_addresses

## Campos

```sql
id
tenant_id
patient_id
zip_code
street
number
district
city
state
country
created_at
```

---

# 19. TABELA: patient_contacts

## Campos

```sql
id
tenant_id
patient_id
name
relationship
phone
email
created_at
```

---

# 20. TABELA: patient_documents

## Campos

```sql
id
tenant_id
patient_id
type
file_url
file_name
mime_type
created_by
created_at
```

---

# 21. PROFISSIONAIS

## Objetivo

Gerenciar profissionais da clínica.

---

# 22. TABELA: professionals

## Campos

```sql
id
tenant_id
user_id
full_name
specialty
license_number
document
phone
email
status
created_at
updated_at
```

---

# 23. TABELA: specialties

## Campos

```sql
id
tenant_id
name
description
created_at
```

---

# 24. AGENDA

## Objetivo

Gerenciar consultas e atendimentos.

---

# 25. TABELA: appointments

## Campos

```sql
id
tenant_id
patient_id
professional_id
unit_id
room_id
appointment_type_id
status
scheduled_start
scheduled_end
confirmed_at
cancelled_at
cancel_reason
notes
created_by
created_at
updated_at
```

---

# 26. TABELA: appointment_types

## Campos

```sql
id
tenant_id
name
duration_minutes
color
created_at
```

---

# 27. TABELA: appointment_status_history

## Campos

```sql
id
tenant_id
appointment_id
old_status
new_status
changed_by
changed_at
```

---

# 28. PRONTUÁRIO

## Objetivo

Centralizar histórico clínico.

---

# 29. TABELA: medical_records

## Campos

```sql
id
tenant_id
patient_id
professional_id
appointment_id
record_type
content
created_at
updated_at
```

---

# 30. TABELA: evolutions

## Campos

```sql
id
tenant_id
medical_record_id
professional_id
content
created_at
```

---

# 31. TABELA: prescriptions

## Campos

```sql
id
tenant_id
patient_id
professional_id
appointment_id
content
created_at
```

---

# 32. TABELA: exams

## Campos

```sql
id
tenant_id
patient_id
professional_id
appointment_id
exam_type
status
requested_at
completed_at
result_url
created_at
```

---

# 33. FINANCEIRO

## Objetivo

Centralizar operação financeira.

---

# 34. TABELA: financial_categories

## Campos

```sql
id
tenant_id
name
type
created_at
```

---

# 35. TABELA: accounts_receivable

## Campos

```sql
id
tenant_id
patient_id
appointment_id
category_id
amount
discount
interest
due_date
paid_at
status
created_at
updated_at
```

---

# 36. TABELA: accounts_payable

## Campos

```sql
id
tenant_id
supplier_id
category_id
description
amount
due_date
paid_at
status
created_at
```

---

# 37. TABELA: payments

## Campos

```sql
id
tenant_id
receivable_id
payment_method
amount
paid_at
transaction_reference
created_at
```

---

# 38. CRM

## Objetivo

Relacionamento com pacientes.

---

# 39. TABELA: crm_interactions

## Campos

```sql
id
tenant_id
patient_id
channel
type
content
created_by
created_at
```

---

# 40. COMUNICAÇÃO

## Objetivo

Centralizar mensagens e automações.

---

# 41. TABELA: whatsapp_messages

## Campos

```sql
id
tenant_id
patient_id
appointment_id
phone
direction
template_name
content
status
provider_message_id
sent_at
delivered_at
read_at
created_at
```

---

# 42. TABELA: email_messages

## Campos

```sql
id
tenant_id
patient_id
subject
content
status
sent_at
created_at
```

---

# 43. LABORATÓRIO

## Objetivo

Gerenciar exames laboratoriais.

---

# 44. TABELA: laboratory_orders

## Campos

```sql
id
tenant_id
patient_id
professional_id
status
requested_at
completed_at
created_at
```

---

# 45. TABELA: laboratory_results

## Campos

```sql
id
tenant_id
order_id
exam_name
result
reference_value
status
released_at
created_at
```

---

# 46. ESTOQUE

## Objetivo

Controle operacional de estoque.

---

# 47. TABELA: stock_items

## Campos

```sql
id
tenant_id
name
sku
quantity
minimum_quantity
unit
created_at
updated_at
```

---

# 48. TABELA: stock_movements

## Campos

```sql
id
tenant_id
item_id
movement_type
quantity
reason
created_by
created_at
```

---

# 49. UNIDADES

## Objetivo

Suporte multiunidade.

---

# 50. TABELA: units

## Campos

```sql
id
tenant_id
name
document
phone
email
created_at
```

---

# 51. TABELA: rooms

## Campos

```sql
id
tenant_id
unit_id
name
type
status
created_at
```

---

# 52. AUDITORIA

## Objetivo

Registrar ações críticas.

---

# 53. TABELA: audit_logs

## Campos

```sql
id
tenant_id
user_id
entity_type
entity_id
action
old_data
new_data
ip_address
user_agent
created_at
```

---

# 54. AUTOMAÇÕES

## Objetivo

Executar workflows automáticos.

---

# 55. TABELA: automation_workflows

## Campos

```sql
id
tenant_id
name
trigger_event
is_active
created_at
```

---

# 56. TABELA: automation_executions

## Campos

```sql
id
tenant_id
workflow_id
status
started_at
finished_at
logs
```

---

# 57. NOTIFICAÇÕES

## Objetivo

Gerenciar notificações internas.

---

# 58. TABELA: notifications

## Campos

```sql
id
tenant_id
user_id
title
content
type
is_read
created_at
```

---

# 59. CONFIGURAÇÕES

## Objetivo

Configurações da clínica.

---

# 60. TABELA: settings

## Campos

```sql
id
tenant_id
key
value
created_at
updated_at
```

---

# 61. RELACIONAMENTOS IMPORTANTES

## Principais relações

```txt
Tenant -> Users
Tenant -> Patients
Tenant -> Appointments
Tenant -> Financial
Tenant -> Laboratory
Tenant -> CRM
Tenant -> WhatsApp
```

---

# 62. ÍNDICES IMPORTANTES

## Priorizar índices para

* tenant_id
* cpf
* status
* datas
* foreign keys
* buscas frequentes

---

# 63. PERFORMANCE

Toda modelagem deve considerar:

* paginação
* índices
* queries otimizadas
* select_related
* prefetch_related
* baixo custo de consulta

---

# 64. SEGURANÇA

Toda modelagem deve considerar:

* LGPD
* isolamento
* auditoria
* proteção de dados
* rastreabilidade

---

# 65. EVENTOS FUTUROS

O schema deve permitir:

* eventos
* automações
* workflows
* analytics
* IA
* integrações

---

# 66. ESCALABILIDADE

A modelagem deve permitir:

* crescimento gradual
* multiunidades
* expansão enterprise
* integrações futuras
* mobile
* BI

---

# 67. OBJETIVO DO DATABASE SCHEMA

Construir uma fundação:

* sólida
* escalável
* organizada
* auditável
* segura
* preparada para crescimento
* preparada para operação enterprise
