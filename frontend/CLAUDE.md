# Frontend — CLAUDE.md

> Diretrizes específicas para desenvolvimento frontend do Clinix.

---

# Objetivo

O frontend do Clinix deve transmitir:

* modernidade
* sofisticação
* velocidade
* clareza
* organização
* experiência premium
* fluidez operacional

O sistema deve parecer:

* profissional
* confiável
* moderno
* clean
* elegante
* rápido
* intuitivo

---

# Stack Oficial

* Next.js
* React
* TypeScript
* Tailwind CSS
* Shadcn/UI
* TanStack Query
* Zustand
* React Hook Form
* Zod

---

# Filosofia Frontend

O frontend do Clinix NÃO deve parecer:

* ERP legado
* sistema poluído
* painel antigo
* sistema visualmente pesado
* interface confusa
* dashboard genérico

---

# O frontend deve priorizar

* UX premium
* baixa carga cognitiva
* simplicidade operacional
* velocidade
* acessibilidade
* componentização
* consistência visual
* excelente experiência em telas grandes

---

# Design Philosophy

O Clinix deve transmitir:

* tecnologia
* organização
* confiança
* sofisticação
* produtividade
* fluidez operacional

---

# Referências Visuais

Referências desejadas:

* Linear
* Notion
* Stripe
* Vercel
* Raycast
* Slack
* Framer
* Ramp

---

# Arquitetura Frontend

O frontend deve ser:

* modular
* desacoplado
* componentizado
* API-first
* escalável
* mobile-ready

---

# Estrutura Recomendada

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

# Organização

## app/

Responsável por:

* rotas
* layouts
* providers
* páginas

---

## components/

Responsável por:

* componentes reutilizáveis
* UI compartilhada
* design system

---

## modules/

Responsável por:

* funcionalidades isoladas
* regras visuais de domínio
* telas específicas

---

## services/

Responsável por:

* comunicação com APIs
* requests
* clients
* interceptors

---

## hooks/

Responsável por:

* hooks reutilizáveis
* lógica compartilhada
* estados derivados

---

## stores/

Responsável por:

* estado global
* autenticação
* preferências
* estados persistentes

---

# Componentização

Toda UI deve ser componentizada.

---

# Priorizar

* reutilização
* legibilidade
* desacoplamento
* simplicidade
* consistência

---

# Nunca permitir

* componentes gigantes
* duplicação
* lógica espalhada
* telas monolíticas

---

# UI/UX

Toda interface deve priorizar:

* clareza
* velocidade
* produtividade
* previsibilidade
* simplicidade

---

# UX Operacional

O sistema será usado diariamente por:

* recepcionistas
* médicos
* dentistas
* fisioterapeutas
* psicólogos
* biomédicos
* financeiro
* gestores

---

# Portanto a UX deve ser

* rápida
* objetiva
* fluida
* intuitiva
* eficiente

---

# Visual

## Priorizar

* espaços bem definidos
* grids organizados
* tipografia limpa
* bordas suaves
* sombras leves
* hierarquia visual forte

---

# Evitar

* excesso de cores
* excesso de informação
* visual poluído
* dashboards exagerados
* elementos desnecessários

---

# Design System

Toda interface deve seguir:

* spacing consistente
* tipografia consistente
* componentes padronizados
* estados visuais padronizados

---

# Estados de Interface

Toda tela deve considerar:

* loading
* empty state
* error state
* success state
* skeleton loading

---

# Formulários

Todos os formulários devem:

* possuir validação
* feedback visual
* mensagens claras
* UX fluida
* acessibilidade

---

# Stack de Formulários

* React Hook Form
* Zod

---

# Data Fetching

Toda comunicação servidor deve utilizar:

# TanStack Query

---

# Benefícios

* cache
* sincronização
* invalação inteligente
* retries
* loading states

---

# Estado Global

Estado global deve usar:

# Zustand

---

# Nunca usar estado global para

* dados temporários desnecessários
* listas gigantes
* cache de APIs

---

# Tipagem

Todo frontend deve utilizar:

# TypeScript rigorosamente

---

# Nunca permitir

* any desnecessário
* tipagem ignorada
* payloads sem tipos

---

# APIs

Frontend deve consumir APIs desacopladas.

---

# Nunca permitir

* lógica de backend no frontend
* regras críticas no cliente
* validações críticas apenas no frontend

---

# Segurança

O frontend deve considerar:

* RBAC
* permissões visuais
* proteção de rotas
* controle de sessão
* autenticação segura

---

# Multi-Tenancy

Toda interface deve considerar tenant.

---

# Nunca permitir

* troca indevida de tenant
* vazamento visual
* dados incorretos entre clínicas

---

# Performance

Toda implementação deve considerar:

* lazy loading
* component splitting
* memoização quando necessário
* otimização de renderizações
* carregamento progressivo

---

# Evitar

* re-renderizações excessivas
* telas pesadas
* requests desnecessários
* componentes enormes

---

# Tabelas

As tabelas devem possuir:

* paginação
* busca
* filtros
* ordenação
* performance
* loading states

---

# Dashboards

Dashboards devem priorizar:

* clareza
* métricas úteis
* leitura rápida
* hierarquia visual

---

# Sidebar

Sidebar deve ser:

* limpa
* organizada
* clara
* escalável

---

# Navegação

A navegação deve ser:

* intuitiva
* rápida
* consistente
* previsível

---

# Responsividade

O sistema deve funcionar bem em:

* desktops
* notebooks
* tablets
* mobile futuramente

---

# Mobile-Ready

Mesmo sendo web-first inicialmente:

Toda interface deve nascer preparada para mobile.

---

# Acessibilidade

Toda interface deve considerar:

* contraste
* navegação por teclado
* foco visual
* labels corretos
* feedbacks acessíveis

---

# Feedbacks Visuais

Toda ação deve possuir:

* feedback visual
* loading
* sucesso
* erro
* confirmação

---

# Notificações

Utilizar notificações claras e discretas.

Evitar:

* excesso de modais
* excesso de alerts
* interrupções desnecessárias

---

# Padrões Visuais

## Inputs

Devem ser:

* limpos
* claros
* consistentes

---

## Botões

Devem transmitir:

* clareza
* prioridade
* hierarquia

---

## Modais

Devem ser usados apenas quando necessário.

---

# Empty States

Toda tela sem dados deve possuir:

* orientação clara
* CTA útil
* UX amigável

---

# Loading States

Evitar:

* telas piscando
* mudanças bruscas
* loaders excessivos

Priorizar:

* skeleton loading
* carregamento progressivo

---

# Organização de Código

Priorizar:

* arquivos pequenos
* componentes reutilizáveis
* nomes claros
* módulos organizados

---

# Evitar

Nunca:

* componentes gigantes
* lógica duplicada
* estilos inconsistentes
* acoplamento excessivo

---

# Padrões de Código

Priorizar:

* legibilidade
* simplicidade
* manutenção
* consistência

---

# Qualidade Visual

O Clinix deve parecer:

* SaaS premium
* software enterprise moderno
* plataforma tecnológica de alto nível

---

# Sensação do Produto

O usuário deve sentir:

* velocidade
* organização
* confiança
* fluidez
* profissionalismo

---

# Objetivo Final

Construir um frontend:

* moderno
* premium
* rápido
* elegante
* escalável
* intuitivo
* altamente produtivo
* visualmente sofisticado

Sem perder:

* simplicidade
* clareza
* performance
* organização
