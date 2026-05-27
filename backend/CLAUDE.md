# Backend — CLAUDE.md

> Diretrizes específicas para desenvolvimento backend do Clinix.

---

# Objetivo

O backend do Clinix deve ser:

* escalável
* modular
* desacoplado
* seguro
* performático
* multi-tenant
* auditável
* preparado para IA
* preparado para mobile
* preparado para integrações

---

# Stack Oficial

* Python
* Django
* Django REST Framework
* PostgreSQL
* Redis
* Celery
* Django Channels

---

# Arquitetura

O backend deve seguir:

* Modular Monolith
* API-first
* Event-driven
* Services-oriented architecture

---

# Estrutura Base

```txt
backend/

apps/
core/
pacientes/
agenda/
prontuario/
financeiro/
comunicacao/
laboratorio/
crm/
automacoes/

shared/
services/
integrations/
events/
utils/
```

---

# Princípios Obrigatórios

Toda implementação backend deve priorizar:

* legibilidade
* separação de responsabilidades
* baixo acoplamento
* alta coesão
* rastreabilidade
* segurança
* performance
* manutenibilidade

---

# Multi-Tenancy

## Regra crítica

Toda entidade deve considerar tenant.

---

## Regras obrigatórias

* tenant_id obrigatório
* queries contextualizadas
* isolamento obrigatório
* validação de tenant em APIs
* permissões contextualizadas

---

## Nunca permitir

* vazamento de dados
* queries sem tenant
* bypass de isolamento
* acesso entre clínicas

---

# Regras de Negócio

Regras de negócio devem ficar:

* services
* domain services
* workflows

Nunca:

* em views
* em serializers complexos
* espalhadas pelo sistema

---

# Views

Views devem ser finas.

Responsabilidades:

* autenticação
* autorização
* validação
* orquestração
* resposta HTTP

Nunca:

* regras complexas
* lógica pesada
* queries desnecessárias

---

# Services

Services devem centralizar:

* regras de negócio
* fluxos operacionais
* validações complexas
* automações
* integrações

---

# Models

Models devem:

* ser simples
* representar domínio
* possuir relacionamentos claros
* evitar lógica excessiva

---

# Serializers

Serializers devem:

* validar dados
* transformar payloads
* manter simplicidade

Evitar:

* lógica operacional
* side-effects complexos

---

# Permissões

Todo endpoint deve validar:

* autenticação
* tenant
* RBAC
* contexto operacional

---

# RBAC

Permissões devem considerar:

* tenant
* módulo
* ação
* unidade
* papel do usuário

---

# Auditoria

Toda ação crítica deve gerar logs.

---

## Exemplos

* login
* alterações clínicas
* alterações financeiras
* exclusões
* permissões
* cancelamentos

---

# Soft Delete

Entidades críticas devem considerar soft delete.

Exemplos:

* pacientes
* documentos
* registros clínicos

---

# APIs

As APIs devem ser:

* RESTful
* previsíveis
* consistentes
* versionáveis
* documentadas

---

# Padrões de API

Toda API deve considerar:

* paginação
* filtros
* ordenação
* autenticação
* tenancy
* auditoria
* performance

---

# Versionamento

Padrão:

```txt
/api/v1/
```

---

# Paginação

Listagens devem possuir:

* paginação
* filtros
* busca
* ordenação

---

# Performance

Toda implementação deve considerar:

* select_related
* prefetch_related
* índices
* cache
* paginação
* queries eficientes

---

# Nunca permitir

* N+1 queries
* queries desnecessárias
* loops pesados
* carregamentos gigantes

---

# Cache

Redis será utilizado para:

* cache
* sessões
* filas
* rate limiting
* dados temporários

---

# Processamento Assíncrono

Operações pesadas devem utilizar:

* Celery
* Redis

---

## Exemplos

* WhatsApp
* e-mails
* relatórios
* PDFs
* automações
* integrações

---

# Eventos

O backend deve ser preparado para eventos.

---

## Exemplos

* consulta_criada
* consulta_cancelada
* pagamento_confirmado
* exame_liberado
* paciente_cadastrado

---

# Objetivos dos Eventos

Permitir:

* automações
* integrações
* notificações
* workflows
* analytics
* IA

---

# Realtime

Realtime deve utilizar:

* Django Channels
* WebSockets

---

# Banco de Dados

## Banco oficial

PostgreSQL

---

# Modelagem

Toda modelagem deve considerar:

* tenant
* auditoria
* timestamps
* performance
* integridade

---

# Campos padrão

Entidades principais devem possuir:

```python
id
tenant_id
created_at
updated_at
```

---

# Segurança

O backend manipula dados sensíveis.

Toda implementação deve considerar:

* LGPD
* RBAC
* auditoria
* rastreabilidade
* proteção de dados
* autorização segura

---

# Nunca permitir

* bypass de permissões
* exposição de dados
* endpoints inseguros
* acesso sem tenant
* dados sensíveis sem proteção

---

# Integrações

Integrações devem ser:

* desacopladas
* resilientes
* rastreáveis
* assíncronas quando possível

---

# WhatsApp

WhatsApp é parte central do produto.

Toda arquitetura deve considerar:

* filas
* retries
* templates
* rastreamento
* histórico
* automações

---

# IA

O backend deve nascer preparado para IA.

---

# Futuras aplicações

* sumarização
* insights
* classificação automática
* assistentes internos
* análise operacional

---

# Logs

O sistema deve possuir logs estruturados.

---

# Monitoramento

Preparar arquitetura para:

* Sentry
* Prometheus
* Grafana

---

# Testes

Todo módulo crítico deve possuir testes.

---

# Backend Tests

Priorizar:

* services
* regras de negócio
* permissões
* APIs críticas
* workflows

---

# Organização

Priorizar:

* arquivos pequenos
* módulos claros
* responsabilidades isoladas
* nomes consistentes

---

# Evitar

Nunca:

* arquivos gigantes
* services monolíticos
* lógica duplicada
* regras espalhadas
* acoplamento excessivo

---

# Filosofia Backend

O backend do Clinix deve transmitir:

* robustez
* organização
* previsibilidade
* segurança
* escalabilidade
* modernidade

---

# Objetivo Final

Construir uma fundação backend:

* sólida
* escalável
* segura
* desacoplada
* preparada para crescimento
* preparada para IA
* preparada para mobile
* preparada para operação enterprise
