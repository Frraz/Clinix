# ADR 002 — Multi-Tenancy Row-Based

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix é uma plataforma SaaS multi-clínica.

Dezenas a centenas de clínicas (tenants) compartilharão a mesma infraestrutura.

Os dados são clínicos e financeiros — sensíveis, com obrigação legal de isolamento (LGPD, CFM).

Três estratégias clássicas existem:

* **Database por tenant** — isolamento máximo, custo alto, gestão complexa
* **Schema por tenant** (django-tenants) — isolamento forte no banco, complexidade média
* **Row-based** (`tenant_id` em cada tabela) — isolamento por filtro, simplicidade alta

---

# Decisão

Adotar **multi-tenancy row-based**:

* todas as tabelas operacionais terão coluna `tenant_id`
* middleware resolve o tenant a partir do JWT/subdomínio
* `TenantAwareManager` filtra automaticamente toda query
* `TenantAwareModel` como base abstrata de todos os modelos
* PostgreSQL Row-Level Security ativado nas tabelas mais críticas (prontuários, prescrições, exames, pagamentos)
* testes de isolamento obrigatórios no CI

---

# Consequências

## Positivas

* uma única base de dados, uma única suite de migrations
* backups, analytics SaaS e operações de billing triviais
* connection pool eficiente mesmo com muitos tenants
* tooling padrão (PgAdmin, dumps, índices) funciona sem ajuste
* curva de aprendizado baixa para o time
* migração futura para schema-based ou DB dedicado por enterprise client é possível adicionando coluna de roteamento

---

## Negativas

* risco principal: vazamento por filtro esquecido
* exige disciplina rigorosa em todo código que faz query
* exige cultura de code review focada em tenancy

---

## Mitigações

* `TenantAwareManager` como manager padrão — bloqueia `.objects.all()` sem contexto
* assertion em desenvolvimento para detectar queries sem tenant
* PostgreSQL RLS como segunda camada de defesa em tabelas críticas
* testes automatizados marcados `pytest -m tenancy` rodam no CI
* code review obrigatório destaca queries cross-tenant

---

# Alternativas consideradas

## django-tenants (schema-based)

Descartado para MVP:

* exige rodar migrations em N schemas
* connection pool sofre com centenas de tenants
* queries cross-tenant (analytics SaaS, billing, super-admin) ficam complicadas
* dificulta operações simples de DBA

Reavaliar para enterprise clients no futuro.

---

## DB por tenant

Descartado:

* custo de infraestrutura inviável no MVP
* complexidade operacional desproporcional
* só faz sentido para clientes enterprise dedicados (fase futura)

---

# Referências

* `docs/tenancy.md`
* `docs/database-schema.md`
* `docs/stack.md` (seção Multi-tenancy)
