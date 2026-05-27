# Infrastructure — CLAUDE.md

> Diretrizes específicas de infraestrutura, DevOps, deploy, observabilidade e escalabilidade do Clinix.

---

# Objetivo

A infraestrutura do Clinix deve ser:

* escalável
* resiliente
* segura
* observável
* automatizada
* simples de operar
* preparada para crescimento gradual

---

# Filosofia de Infraestrutura

A infraestrutura deve priorizar:

* simplicidade operacional
* automação
* estabilidade
* monitoramento
* baixo custo inicial
* escalabilidade futura
* segurança
* recuperação rápida

---

# Estratégia Inicial

Inicialmente o Clinix deve possuir infraestrutura:

* simples
* organizada
* previsível
* fácil de manter
* fácil de evoluir

---

# Estratégia de Evolução

A infraestrutura deve permitir evolução futura para:

* múltiplos servidores
* autoscaling
* workers dedicados
* Kubernetes
* serviços distribuídos
* arquitetura cloud-native

Sem necessidade de reestruturação completa.

---

# Stack de Infraestrutura

## Inicial

* Docker
* Docker Compose
* Nginx
* PostgreSQL
* Redis
* Celery
* S3 / Cloudflare R2

---

# Infraestrutura Base

## Containers

Toda aplicação deve rodar em containers.

---

## Benefícios

* padronização
* isolamento
* previsibilidade
* facilidade de deploy
* facilidade de desenvolvimento

---

# Docker

Todo serviço deve possuir:

* Dockerfile otimizado
* ambiente consistente
* separação de responsabilidades
* build previsível

---

# Docker Compose

Inicialmente será utilizado para:

* desenvolvimento
* homologação
* ambiente local
* orquestração inicial

---

# Serviços Esperados

## Backend

* Django API
* workers Celery
* scheduler Celery Beat

---

## Frontend

* Next.js

---

## Infraestrutura

* PostgreSQL
* Redis
* Nginx

---

# Proxy Reverso

## Nginx

Responsável por:

* proxy reverso
* SSL
* cache
* compressão
* roteamento
* otimizações HTTP

---

# Banco de Dados

## PostgreSQL

Banco principal oficial do sistema.

---

# Requisitos

* backups
* índices
* monitoramento
* replicação futura
* segurança
* performance

---

# Redis

Responsável por:

* cache
* filas
* sessões
* rate limiting
* realtime
* dados temporários

---

# Processamento Assíncrono

## Celery

Responsável por:

* filas
* automações
* tarefas pesadas
* integração WhatsApp
* e-mails
* geração de relatórios
* workflows

---

# Storage

## Arquivos e documentos

Devem utilizar:

* S3
* Cloudflare R2

---

# Exemplos

* exames
* imagens
* anexos
* documentos
* PDFs

---

# Nunca armazenar

* uploads críticos localmente em produção
* arquivos permanentes dentro do container

---

# Ambientes

O projeto deve possuir:

* development
* staging
* production

---

# Variáveis de Ambiente

Toda configuração sensível deve utilizar:

# ENV VARIABLES

---

# Nunca permitir

* secrets hardcoded
* credenciais no código
* tokens expostos

---

# Arquivos esperados

```txt id="m2t8lb"
.env
.env.example
```

---

# Segurança

Toda infraestrutura deve considerar:

* LGPD
* proteção de dados
* isolamento
* backups
* criptografia
* autenticação segura

---

# HTTPS

Produção deve utilizar:

# HTTPS obrigatório

---

# Certificados

Priorizar:

* Let's Encrypt
* Cloudflare SSL

---

# Cloudflare

Cloudflare será utilizado para:

* CDN
* proteção
* SSL
* cache
* segurança
* rate limiting
* proteção DDoS

---

# Logs

Toda infraestrutura deve possuir logs.

---

# Logs importantes

* backend
* frontend
* nginx
* workers
* autenticação
* auditoria
* filas

---

# Monitoramento

A infraestrutura deve ser preparada para:

* Sentry
* Prometheus
* Grafana

---

# Objetivos

* observabilidade
* rastreamento
* debugging
* métricas
* alertas

---

# Métricas Importantes

Monitorar:

* uso de CPU
* memória
* filas
* requests
* erros
* tempo de resposta
* uso do banco
* workers

---

# Alertas

O sistema deve possuir alertas para:

* erros críticos
* workers parados
* filas acumuladas
* falhas de deploy
* indisponibilidade

---

# Backups

Backups devem ser:

* automáticos
* frequentes
* seguros
* testados periodicamente

---

# Itens críticos para backup

* PostgreSQL
* uploads
* documentos
* storage crítico

---

# Deploy

Deploy deve priorizar:

* previsibilidade
* rollback simples
* automação
* baixa indisponibilidade

---

# Estratégia Inicial de Deploy

Inicialmente:

* VPS
* Docker Compose
* CI/CD simples

---

# Estratégia Futura

Futuramente:

* Kubernetes
* múltiplos servidores
* autoscaling
* deploy distribuído

---

# CI/CD

Pipeline deve automatizar:

* testes
* lint
* build
* deploy
* validações

---

# Nunca permitir

* deploy manual inseguro
* deploy sem validação
* alterações sem rastreabilidade

---

# Git Strategy

Priorizar:

* branches organizadas
* pull requests
* revisão de código
* histórico limpo

---

# Ambientes Isolados

Nunca compartilhar:

* banco de produção
* secrets
* credenciais
* buckets

Entre ambientes.

---

# Performance

Toda infraestrutura deve considerar:

* cache
* compressão
* otimização HTTP
* filas
* escalabilidade
* CDN

---

# Escalabilidade

A infraestrutura deve permitir:

* horizontalização
* múltiplos workers
* múltiplas instâncias
* crescimento gradual

---

# Rate Limiting

Implementar proteção para:

* brute force
* spam
* abuso de APIs
* ataques automatizados

---

# Realtime

Realtime deve suportar:

* WebSockets
* notificações live
* atualizações operacionais

---

# Workers

Workers devem ser separados por responsabilidade quando necessário.

---

# Exemplos futuros

* WhatsApp workers
* e-mail workers
* relatórios
* IA
* integrações

---

# Observabilidade

Toda falha importante deve ser rastreável.

---

# Objetivos

* identificar problemas rapidamente
* facilitar debugging
* reduzir downtime
* melhorar estabilidade

---

# Disaster Recovery

O projeto deve considerar:

* backups
* rollback
* recuperação rápida
* redundância futura

---

# Custos

Inicialmente priorizar:

* baixo custo
* simplicidade
* eficiência operacional

Sem sacrificar:

* segurança
* qualidade
* escalabilidade

---

# Organização

Infraestrutura deve possuir:

* arquivos organizados
* docker limpo
* compose organizado
* variáveis claras
* documentação

---

# Nunca permitir

* infraestrutura improvisada
* configurações espalhadas
* duplicação
* containers gigantes
* configurações inseguras

---

# Filosofia DevOps

Infraestrutura deve transmitir:

* organização
* previsibilidade
* estabilidade
* segurança
* profissionalismo

---

# Objetivo Final

Construir uma infraestrutura:

* moderna
* segura
* resiliente
* escalável
* observável
* preparada para crescimento
* preparada para operação enterprise

Sem perder:

* simplicidade
* previsibilidade
* baixo custo operacional inicial
