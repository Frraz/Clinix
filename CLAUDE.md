# CLAUDE.md — Clinix

## Sobre o Projeto

Clinix é uma plataforma SaaS para gestão de clínicas, laboratórios e operações de saúde.

O sistema será:

* multi-tenant
* API-first
* modular
* mobile-ready
* altamente escalável
* orientado a automações
* preparado para IA

---

# Objetivo do Produto

Construir uma plataforma moderna e robusta para:

* clínicas médicas
* odontologia
* fisioterapia
* psicologia
* laboratórios
* biomédicos
* operações multidisciplinares

Priorizar:

* UX premium
* performance
* automações
* segurança
* simplicidade operacional
* escalabilidade

---

# Stack Oficial

## Backend

* Python
* Django
* Django REST Framework
* PostgreSQL
* Redis
* Celery

## Frontend

* Next.js
* TypeScript
* Tailwind
* Shadcn/UI

## Infraestrutura

* Docker
* Nginx
* S3/R2
* CI/CD

---

# Regras Arquiteturais

* Arquitetura modular
* API-first
* Multi-tenant obrigatório
* Separação clara de responsabilidades
* Serviços desacoplados
* Componentização
* Código escalável e legível

---

# Multi-Tenancy

Toda entidade deve considerar tenant.

Regras obrigatórias:

* todo recurso pertence a uma clínica
* toda API deve validar tenant
* nunca permitir vazamento entre tenants
* clinic_id deve ser considerado em toda modelagem

---

# Segurança

Prioridades obrigatórias:

* LGPD
* RBAC
* auditoria
* autenticação segura
* rastreabilidade
* proteção de dados médicos

Nunca:

* expor dados sensíveis
* ignorar permissões
* criar endpoints inseguros

---

# Backend

Priorizar:

* services
* baixo acoplamento
* alta coesão
* organização modular
* tipagem
* performance

Evitar:

* lógica complexa em views
* duplicação
* queries N+1
* funções gigantes
* regras espalhadas

---

# Frontend

Priorizar:

* UX limpa
* rapidez
* acessibilidade
* responsividade
* consistência visual
* produtividade operacional

Evitar:

* telas poluídas
* excesso de modais
* fluxos confusos
* componentes gigantes

---

# Performance

Toda implementação deve considerar:

* cache
* paginação
* lazy loading
* workers assíncronos
* filas
* otimização de queries

---

# Auditoria

Ações críticas devem gerar logs.

Exemplos:

* login
* alterações financeiras
* alterações em prontuários
* permissões
* exclusões

---

# Eventos e Automações

O sistema deve ser preparado para:

* eventos
* filas
* automações
* integrações
* notificações
* workflows

---

# WhatsApp

WhatsApp é parte central do produto.

Toda arquitetura deve considerar:

* automações
* templates
* filas
* histórico
* rastreamento

---

# IA

O sistema deve ser preparado para:

* sumarização
* assistentes internos
* insights operacionais
* automações inteligentes
* classificação automática

---

# Qualidade de Código

Seguir:

* SOLID
* DRY
* KISS
* Separation of Concerns

Nunca:

* gambiarra
* hardcodes desnecessários
* código duplicado
* acoplamento excessivo

---

# UX/UI

O Clinix deve transmitir:

* modernidade
* confiança
* velocidade
* clareza
* sofisticação
* experiência premium

---

# Diretrizes para o Claude Code

Ao trabalhar neste projeto:

* mantenha consistência arquitetural
* respeite tenancy
* respeite RBAC
* respeite LGPD
* priorize simplicidade operacional
* evite overengineering desnecessário
* priorize legibilidade e manutenção

Antes de implementar:

1. validar arquitetura
2. validar regras de negócio
3. validar segurança
4. validar UX
5. validar escalabilidade

---

# Estrutura de Documentação

Consultar documentação complementar em:

* docs/
* ADRs/
* tasks/
* examples/

Arquivos importantes:

* docs/regras-de-negocio.md
* docs/database-schema.md
* docs/arquitetura.md
* docs/funcionalidades.md
* docs/stack.md
* docs/auth.md
* docs/tenancy.md
