# ADR 004 — Next.js como frontend web

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix exige UX de altíssima qualidade:

* agenda com drag-and-drop
* prontuário com editor rich-text
* dashboards interativos
* realtime (notificações, status de atendimento)
* tabelas grandes com performance
* responsividade premium

A escolha do framework frontend determina:

* produtividade do time
* qualidade percebida pelo usuário
* facilidade de reaproveitamento para mobile (React Native)
* curva de aprendizado

---

# Decisão

Adotar **Next.js 15** com:

* App Router
* React Server Components
* TypeScript estrito
* Tailwind CSS 4
* shadcn/ui como base de componentes
* TanStack Query v5 para server state
* Zustand para estado global leve
* React Hook Form + Zod para formulários

Frontend separado do backend Django, consumindo API REST.

---

# Consequências

## Positivas

* SSR e RSC entregam performance superior em telas críticas
* App Router moderno e produtivo
* ecossistema React reaproveitado no mobile (React Native)
* TypeScript elimina classe de bugs em dados clínicos
* shadcn/ui dá componentes acessíveis e customizáveis sem vendor lock-in
* deploy simples (Node ou Edge)
* enorme comunidade

---

## Negativas

* mais complexidade de deploy comparado a Django Templates
* dois deploys para coordenar (web + API)
* exige time confortável com React/TS

---

## Mitigações

* Docker Compose padroniza ambiente local
* CI/CD único para os dois lados
* contratos TypeScript gerados a partir do OpenAPI

---

# Alternativas consideradas

## Django Templates + HTMX/Alpine

Descartado: insuficiente para a interatividade exigida (drag-and-drop, editor rich-text, realtime). Ver ADR 001.

---

## Vue / Nuxt

Descartado: ecossistema React é necessário para reaproveitamento em React Native (mobile fase 4).

---

## SvelteKit

Descartado: ecossistema menor, menos integrações maduras para o domínio (componentes de calendário, editor médico), e quebraria reuso com React Native.

---

## Remix

Descartado: viável tecnicamente, mas Next.js tem ecossistema mais amplo, especialmente para os componentes que o Clinix consome (FullCalendar, TipTap, Recharts).

---

# Referências

* `docs/stack.md`
* `docs/design-system.md`
* `frontend/CLAUDE.md`
