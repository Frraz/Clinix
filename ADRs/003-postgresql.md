# ADR 003 — PostgreSQL como banco principal

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix precisa de banco de dados que suporte:

* dados relacionais bem estruturados (pacientes, agendamentos, financeiro)
* dados semi-estruturados (prontuário dinâmico por especialidade)
* full-text search em português (busca de pacientes, CID)
* row-level security (camada extra de isolamento)
* índices avançados (GIN, GiST, partial)
* particionamento (auditoria, logs)
* extensões (`pg_trgm`, `pgvector` para IA futura)

Alternativas comuns: MySQL, MongoDB, SQL Server, MariaDB.

---

# Decisão

Adotar **PostgreSQL 16** como banco de dados principal.

* todos os modelos persistidos em PostgreSQL
* JSONB para campos dinâmicos (`MedicalRecord.content`, `Workflow.config`, `Plan.features`)
* `pg_trgm` ativado para busca fuzzy
* full-text search nativo em português
* RLS ativado nas tabelas mais sensíveis
* `pgvector` quando entrar IA com embeddings (Fase 3)

---

# Consequências

## Positivas

* combinação relacional + JSONB cobre prontuário dinâmico sem precisar de banco NoSQL adicional
* RLS reforça multi-tenancy row-based
* ecossistema maduro com Django ORM
* ferramentas robustas (pg_dump, pg_basebackup, pgbouncer)
* extensível para vetores, séries temporais e geoespacial
* gratuito e amplamente suportado

---

## Negativas

* exige conhecimento mais avançado para tuning em alta escala
* requer atenção a VACUUM e bloat
* JSONB sem disciplina vira "schemaless bagunçado"

---

## Mitigações

* schema JSONB versionado e validado em serializers
* monitoramento de queries lentas via `pg_stat_statements`
* backups automáticos diários para R2
* pgbouncer quando o número de conexões crescer

---

# Alternativas consideradas

## MySQL / MariaDB

Descartado:

* JSONB inferior
* sem RLS nativo competitivo
* full-text search em português menos robusto
* extensões geoespaciais/vetoriais menos maduras

---

## MongoDB

Descartado:

* dados clínicos e financeiros são fortemente relacionais
* transações multi-documento ainda têm limitações
* migrations e tipagem ficam frágeis

---

## SQL Server

Descartado:

* custo de licença inviável
* lock-in com Microsoft

---

# Referências

* `docs/database-schema.md`
* `docs/stack.md`
* `docs/tenancy.md`
