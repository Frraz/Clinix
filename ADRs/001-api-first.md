# ADR 001 — Arquitetura API-First

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix precisará servir:

* aplicação web (Next.js)
* aplicações mobile (iOS + Android via React Native)
* portal do paciente
* integrações externas (laboratórios, ERPs, BI)
* webhooks de eventos
* automações internas

Cada um desses canais precisa do mesmo conjunto de dados e regras de negócio.

A alternativa tradicional Django Templates + algumas APIs avulsas geraria:

* duplicação de lógica entre templates e APIs
* dificuldade para reusar regras no mobile
* fragilidade quando uma nova integração surge
* templates pesados sem suportar interatividade exigida em saúde

---

# Decisão

Adotar arquitetura **API-First**:

* todo o backend exposto como API REST versionada (`/api/v1/`)
* nenhuma lógica de negócio em templates
* nenhum acesso direto ao banco fora dos services
* frontend web consumindo a mesma API que mobile, portal e integrações
* documentação OpenAPI obrigatória
* contratos tipados compartilhados via TypeScript/Zod

---

# Consequências

## Positivas

* uma única fonte de verdade para regras de negócio
* mobile vira esforço pequeno quando o backend já está pronto
* integrações externas naturais
* testabilidade alta (testar API é mais simples que testar templates)
* contratos explícitos forçam consistência

---

## Negativas

* maior complexidade inicial (frontend separado)
* exige disciplina de versionamento e backward compatibility
* curva de aprendizado para quem só conhecia Django tradicional

---

## Trade-offs aceitos

A complexidade inicial é justificada porque:

* mobile é parte da estratégia (fase 4)
* integrações são parte do diferencial competitivo
* UX moderna em saúde exige interatividade que templates não entregam

---

# Alternativas consideradas

## Django Templates + HTMX

Descartado: insuficiente para a interatividade exigida (drag-and-drop de agenda, editor rich-text de prontuário, dashboards realtime).

---

## Backend monolítico com frontend acoplado

Descartado: impede reaproveitamento para mobile e integrações.

---

## GraphQL desde o início

Descartado: complexidade desproporcional para o MVP. Pode ser avaliado em fase futura para casos específicos de BI.

---

# Referências

* `docs/api.md`
* `docs/arquitetura.md`
* `docs/stack.md`
