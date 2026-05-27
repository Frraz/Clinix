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

Construir uma plataforma moderna e robusta capaz de atender **as 8 especialidades-alvo do MVP**:

1. clínicas médicas gerais
2. odontologia
3. fisioterapia
4. psicologia
5. biomedicina / laboratórios
6. dermatologia
7. clínicas de estética
8. nutrição

E preparada para evoluir para outras áreas (centros de diagnóstico, telemedicina, multi-especialidade, redes de clínicas).

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

# Personalização por Tenant

Toda clínica deve poder personalizar:

* logo
* cor primária
* dados da clínica (CNPJ, endereço, contatos, responsável técnico)
* templates de documentos (receitas, atestados, laudos, recibos)
* templates de comunicação (WhatsApp, email)
* portal do paciente
* multi-unidade com branding por unidade

Toda geração de PDF deve aplicar o branding do tenant.

Nunca permitir CSS/HTML arbitrário injetado pelo tenant.

Detalhamento em `docs/personalizacao.md`.

---

# Portabilidade de Dados

O Clinix NUNCA aprisiona o cliente.

Toda funcionalidade de dados deve considerar:

* import (entrada de clínicas vindas de outros sistemas)
* export operacional (CSV, PDF, Excel, iCal)
* export completo (offboarding sem fricção)
* portabilidade LGPD (paciente — art. 18, V)

Schemas de import/export versionados e documentados publicamente.

Detalhamento em `docs/importacao-exportacao.md`.

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

### Base
* docs/vision.md
* docs/arquitetura.md
* docs/stack.md
* docs/regras-de-negocio.md
* docs/funcionalidades.md
* docs/roadmap.md (master index)
* docs/roadmap/ (detalhamento por fase)
* docs/project-map.md

### Técnica
* docs/api.md
* docs/auth.md
* docs/tenancy.md
* docs/database-schema.md
* docs/design-system.md
* docs/performance.md
* docs/backup-dr.md

### Módulos de domínio
* docs/prontuario.md
* docs/laboratorio.md
* docs/whatsapp.md
* docs/convenios.md
* docs/estoque.md
* docs/crm.md
* docs/portal-paciente.md
* docs/workflows.md
* docs/notificacoes.md
* docs/mobile.md
* docs/ia.md

### Especialidades específicas
* docs/dermatologia.md
* docs/estetica.md
* docs/nutricao.md

### Componentes transversais
* docs/templates-clinicos.md
* docs/imagens-clinicas.md
* docs/pacotes-tratamento.md
* docs/antropometria.md
* docs/odontograma.md
* docs/escalas-testes.md

### Compliance
* docs/lgpd.md
* docs/auditoria.md
* docs/compliance-conselhos.md
* docs/acessibilidade.md

### SaaS e operação
* docs/billing.md
* docs/personalizacao.md
* docs/importacao-exportacao.md
* docs/onboarding.md
* docs/suporte.md

---

# Especialidades-alvo

Toda nova funcionalidade deve ser pensada para servir as 8 especialidades através de:

* prontuário composable (templates por especialidade — ver `docs/templates-clinicos.md`)
* engine única reusada em todas as áreas
* componentes específicos quando exigidos (ver `docs/odontograma.md`, `docs/antropometria.md`)
* schema flexível (JSONB validado) sem duplicar tabelas por especialidade

Nunca:

* criar prontuário hard-coded para uma especialidade
* duplicar lógica por especialidade
* impedir que clínica multi-especialidade habilite mais de uma área
