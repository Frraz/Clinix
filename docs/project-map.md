# Project Map — Clinix

> Mapa estrutural do projeto Clinix.

Este documento define:

* organização geral
* responsabilidades dos diretórios
* arquitetura física do repositório
* padrões estruturais
* separação de responsabilidades

---

# 1. VISÃO GERAL

O Clinix será organizado como um monorepo estruturado.

Objetivos:

* organização
* escalabilidade
* clareza
* produtividade
* desacoplamento
* crescimento sustentável

---

# 2. ESTRUTURA PRINCIPAL

```txt
Clinix/

ADRs/
backend/
frontend/
infrastructure/
docs/
tasks/
examples/

CLAUDE.md
CLAUDE.local.md
README.md
```

---

# 3. ROOT

## Responsabilidade

Centralizar:

* documentação principal
* arquitetura
* visão do projeto
* regras globais
* instruções do Claude
* organização geral

---

# 4. CLAUDE.md

## Objetivo

Arquivo principal de instruções globais para IA.

---

## Responsabilidades

* regras gerais
* padrões do projeto
* filosofia
* arquitetura macro
* diretrizes obrigatórias

---

# 5. CLAUDE.local.md

## Objetivo

Instruções locais e temporárias.

---

## Uso

* experimentos
* contexto temporário
* observações locais
* ajustes específicos

---

# 6. README.md

## Objetivo

Apresentação principal do projeto.

---

## Deve conter

* visão geral
* stack
* arquitetura
* setup
* estrutura
* roadmap
* filosofia

---

# 7. ADRs/

## Objetivo

Architecture Decision Records.

---

# Responsabilidade

Registrar decisões técnicas importantes.

---

# Estrutura

```txt
ADRs/

001-api-first.md
002-multi-tenant.md
003-postgresql.md
004-nextjs.md
005-rbac.md
006-event-driven.md
```

---

# Regras

Toda decisão importante deve possuir ADR.

---

# Exemplos

* troca de stack
* estratégia multi-tenant
* decisões de arquitetura
* autenticação
* infraestrutura

---

# 8. backend/

## Objetivo

Backend principal do sistema.

---

# Responsabilidades

* APIs
* regras de negócio
* tenancy
* autenticação
* integrações
* automações
* processamento operacional

---

# Estrutura sugerida

```txt
backend/

apps/
shared/
services/
events/
integrations/
utils/
```

---

# 9. backend/apps/

## Objetivo

Módulos de domínio.

---

# Estrutura esperada

```txt
apps/

core/
pacientes/
agenda/
prontuario/
financeiro/
crm/
comunicacao/
laboratorio/
automacoes/
```

---

# 10. backend/core/

## Responsável por

* autenticação
* tenancy
* RBAC
* auditoria
* usuários
* configurações

---

# 11. backend/pacientes/

## Responsável por

* cadastro
* documentos
* histórico
* relacionamentos

---

# 12. backend/agenda/

## Responsável por

* consultas
* disponibilidade
* agendamentos
* encaixes
* filas

---

# 13. backend/prontuario/

## Responsável por

* prontuário eletrônico
* evoluções
* prescrições
* exames
* histórico clínico

---

# 14. backend/financeiro/

## Responsável por

* cobranças
* pagamentos
* caixa
* faturamento
* repasses

---

# 15. backend/comunicacao/

## Responsável por

* WhatsApp
* e-mails
* notificações
* templates
* campanhas

---

# 16. backend/laboratorio/

## Responsável por

* exames laboratoriais
* processamento
* resultados
* laudos

---

# 17. backend/crm/

## Responsável por

* relacionamento
* follow-up
* campanhas
* recuperação de pacientes

---

# 18. backend/automacoes/

## Responsável por

* workflows
* eventos
* triggers
* automações operacionais

---

# 19. backend/shared/

## Objetivo

Código compartilhado.

---

# Exemplos

* helpers
* abstrações
* classes base
* mixins
* utilidades comuns

---

# 20. backend/services/

## Objetivo

Services compartilhados.

---

# Exemplos

* PDF
* storage
* notificações
* integrações

---

# 21. backend/events/

## Objetivo

Eventos internos.

---

# Responsabilidades

* dispatch de eventos
* listeners
* integração entre módulos

---

# 22. backend/integrations/

## Objetivo

Integrações externas.

---

# Exemplos

* WhatsApp APIs
* gateways de pagamento
* serviços externos
* ERPs
* laboratórios parceiros

---

# 23. backend/utils/

## Objetivo

Utilidades genéricas.

---

# Exemplos

* formatadores
* helpers pequenos
* funções reutilizáveis

---

# 24. frontend/

## Objetivo

Frontend principal do Clinix.

---

# Responsabilidades

* UX
* UI
* dashboards
* experiência operacional
* consumo de APIs

---

# Estrutura sugerida

```txt
frontend/

app/
components/
modules/
services/
hooks/
stores/
types/
lib/
styles/
```

---

# 25. frontend/app/

## Responsável por

* rotas
* layouts
* páginas
* providers

---

# 26. frontend/components/

## Responsável por

* design system
* UI compartilhada
* componentes reutilizáveis

---

# 27. frontend/modules/

## Responsável por

* funcionalidades isoladas
* módulos visuais
* telas de domínio

---

# 28. frontend/services/

## Responsável por

* comunicação com APIs
* clients HTTP
* interceptors

---

# 29. frontend/hooks/

## Responsável por

* hooks reutilizáveis
* lógica compartilhada

---

# 30. frontend/stores/

## Responsável por

* estado global
* autenticação
* preferências

---

# 31. frontend/types/

## Responsável por

* tipos
* interfaces
* contratos

---

# 32. frontend/lib/

## Responsável por

* bibliotecas internas
* wrappers
* utilidades frontend

---

# 33. frontend/styles/

## Responsável por

* estilos globais
* tokens
* variáveis
* temas

---

# 34. infrastructure/

## Objetivo

Infraestrutura e DevOps.

---

# Responsabilidades

* Docker
* deploy
* CI/CD
* observabilidade
* monitoramento

---

# Estrutura sugerida

```txt
infrastructure/

docker/
nginx/
scripts/
monitoring/
deploy/
```

---

# 35. infrastructure/docker/

## Responsável por

* Dockerfiles
* Compose
* containers

---

# 36. infrastructure/nginx/

## Responsável por

* proxy reverso
* SSL
* cache
* roteamento

---

# 37. infrastructure/scripts/

## Responsável por

* automações
* scripts operacionais
* helpers DevOps

---

# 38. infrastructure/monitoring/

## Responsável por

* Prometheus
* Grafana
* observabilidade
* métricas

---

# 39. infrastructure/deploy/

## Responsável por

* pipelines
* deploys
* automações CI/CD

---

# 40. docs/

## Objetivo

Documentação oficial do projeto.

---

# Responsabilidade

Centralizar:

* visão
* arquitetura
* regras
* stack
* workflows
* funcionalidades

---

# Arquivos principais

```txt
docs/

vision.md
stack.md
arquitetura.md
funcionalidades.md
regras-de-negocio.md
```

---

# Arquivos complementares

```txt
auth.md
tenancy.md
billing.md
whatsapp.md
ia.md
mobile.md
```

---

# 41. tasks/

## Objetivo

Gestão operacional do desenvolvimento.

---

# Arquivos

```txt
backlog.md
mvp.md
bugs.md
sprint-01.md
sprint-02.md
```

---

# Responsabilidades

* roadmap operacional
* organização técnica
* controle de entregas
* priorização

---

# 42. examples/

## Objetivo

Fluxos reais do sistema.

---

# Arquivos

```txt
fluxo-agendamento.md
fluxo-financeiro.md
fluxo-laboratorio.md
fluxo-prontuario.md
fluxo-whatsapp.md
```

---

# Responsabilidade

Documentar:

* jornadas reais
* regras operacionais
* comportamento esperado

---

# 43. PADRÕES GERAIS

Toda estrutura deve priorizar:

* clareza
* separação de responsabilidades
* modularidade
* escalabilidade
* baixo acoplamento

---

# 44. PADRÕES BACKEND

Evitar:

* arquivos gigantes
* lógica espalhada
* módulos acoplados
* services monolíticos

---

# 45. PADRÕES FRONTEND

Evitar:

* componentes gigantes
* páginas poluídas
* duplicação
* estilos inconsistentes

---

# 46. PADRÕES INFRA

Evitar:

* configurações improvisadas
* containers inseguros
* deploy manual sem controle

---

# 47. FILOSOFIA DO REPOSITÓRIO

O projeto deve transmitir:

* organização
* previsibilidade
* profissionalismo
* clareza
* escalabilidade

---

# 48. EVOLUÇÃO FUTURA

A estrutura deve permitir:

* crescimento gradual
* mobile
* IA
* microserviços
* múltiplos times
* operação enterprise

---

# 49. OBJETIVO FINAL

Construir um repositório:

* limpo
* escalável
* profissional
* organizado
* preparado para crescimento
* preparado para operação SaaS enterprise

Sem perder:

* produtividade
* simplicidade
* clareza
* velocidade de desenvolvimento
