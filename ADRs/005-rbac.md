# ADR 005 — RBAC granular (Role-Based Access Control)

* **Status:** Aceito
* **Data:** 2026-05-27
* **Autor:** Time Clinix

---

# Contexto

O Clinix terá perfis muito diferentes operando o mesmo sistema:

* administrador
* gestor
* recepcionista
* médico
* dentista
* fisioterapeuta
* psicólogo
* biomédico
* financeiro
* técnico laboratorial
* DPO

Cada perfil precisa acessar partes diferentes do sistema, com diferentes graus de permissão.

Além disso:

* clínicas multiunidade exigem restrição por unidade
* alguns dados exigem restrição por objeto (ex: psicólogo só vê seus próprios prontuários)
* mesmo usuário pode ter papéis diferentes em clínicas diferentes (multi-tenant)

---

# Decisão

Adotar **RBAC granular** com três camadas:

## Camada 1 — Papel (Role)

Conjunto de permissões nomeado.

Ex: `admin`, `medico`, `recepcionista`.

---

## Camada 2 — Permissão (Permission)

Combinação `modulo.acao`.

Ex: `patients.view`, `patients.create`, `financial.manage`.

---

## Camada 3 — Permissão por objeto (Object Permission)

Permissões específicas em entidades.

Ex: psicólogo Y só vê o prontuário do paciente X.

Implementado com `django-guardian`.

---

# Contexto multi-tenant

Toda permissão é avaliada no contexto do tenant atual.

Mesmo usuário pode ser admin no tenant A e recepcionista no tenant B.

---

# Consequências

## Positivas

* perfis bem definidos cobrem 90% dos casos
* permissões granulares cobrem os 10% restantes
* permissão por objeto resolve sigilo profissional (psicologia, prontuário)
* extensível: novos módulos só precisam declarar suas permissões
* auditável: cada negativa de permissão é registrada

---

## Negativas

* mais complexidade que um modelo de papel único
* requer UI cuidadosa para configurar permissões sem confundir o admin

---

## Mitigações

* roles pré-configuradas no onboarding cobrem casos comuns
* UI de permissões guiada por presets
* code review obrigatório quando um endpoint declara permissões

---

# Implementação

## No backend

```python
permission_classes = [
    IsAuthenticated,
    HasTenantContext,
    HasModulePermission("patients", "view"),
]
```

---

## No frontend

* permissões disponíveis injetadas no JWT (resumo) + endpoint dedicado (detalhe)
* UI esconde ações sem permissão
* nunca confiar apenas no frontend — backend revalida sempre

---

# Alternativas consideradas

## Apenas roles simples (sem permissões granulares)

Descartado: não cobre os casos reais (psicologia, prontuário restrito, multiunidade).

---

## ABAC (Attribute-Based Access Control)

Descartado para MVP: poder demais, complexidade alta. RBAC + object permissions cobre os casos sem virar engine genérico.

---

# Referências

* `docs/auth.md`
* `docs/auditoria.md`
* `backend/CLAUDE.md`
