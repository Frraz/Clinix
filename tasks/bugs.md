# Bugs — Clinix

> Registro de bugs identificados durante o desenvolvimento e operação do Clinix.

---

# 1. COMO USAR

Cada bug deve possuir:

* identificador
* severidade
* status
* descrição clara
* passos para reproduzir
* comportamento esperado
* comportamento atual
* ambiente (dev, staging, prod)
* responsável
* data de abertura
* link para PR/commit de correção (quando resolvido)

---

# 2. SEVERIDADES

* **S0 — Crítico**
  * vazamento de dados entre tenants
  * indisponibilidade total
  * perda de dados clínicos
  * falha de auditoria
  * resposta imediata, sem espera

* **S1 — Alto**
  * funcionalidade central quebrada
  * falha de segurança contornável
  * impacto operacional grande
  * resposta no dia

* **S2 — Médio**
  * funcionalidade secundária quebrada
  * workaround disponível
  * impacto operacional limitado
  * resposta na sprint

* **S3 — Baixo**
  * UX ruim, cosmético
  * sem impacto operacional
  * pode aguardar próxima sprint de polimento

---

# 3. STATUS

* `aberto`
* `em análise`
* `em correção`
* `bloqueado` (dependência externa)
* `validação` (corrigido, aguardando teste)
* `resolvido`
* `não reproduz`
* `não vai corrigir` (com justificativa)

---

# 4. TEMPLATE DE BUG

Copiar e preencher:

```markdown
## BUG-XXX — Título descritivo

* **Severidade:** S0 / S1 / S2 / S3
* **Status:** aberto
* **Ambiente:** dev / staging / prod
* **Responsável:** —
* **Data:** YYYY-MM-DD

### Descrição

O que está acontecendo de errado.

### Passos para reproduzir

1. ...
2. ...
3. ...

### Comportamento esperado

O que deveria acontecer.

### Comportamento atual

O que está acontecendo.

### Logs / evidência

(Stack trace, screenshot, request ID)

### Correção

Link para PR ou commit. Notas pós-correção.
```

---

# 5. BUGS ATIVOS

> Nenhum bug registrado até o momento.
>
> Quando o projeto começar a rodar em ambiente real, esta seção será preenchida.

---

# 6. BUGS RESOLVIDOS (HISTÓRICO)

> Bugs resolvidos ficam registrados aqui para histórico, agrupados por mês.

## 2026-05

— sem registros —

---

# 7. POLÍTICA DE BUGS S0

Qualquer bug **S0** dispara protocolo de incidente:

* notificação imediata ao time
* registro de timeline detalhado
* hotfix priorizado sobre qualquer outra entrega
* post-mortem obrigatório após resolução
* registro em `docs/auditoria.md` se houver impacto a dados

---

# 8. POLÍTICA DE BUGS DE TENANCY

Qualquer bug que **vaze dados entre tenants** é automaticamente **S0**.

Ações:

* desativar feature afetada imediatamente
* notificar tenants impactados
* notificar ANPD se houver exposição de dado pessoal sensível
* corrigir e adicionar teste de regressão obrigatório
* post-mortem público interno

---

# 9. POLÍTICA DE BUGS DE SEGURANÇA

Bugs de segurança seguem fluxo separado:

* não publicados em commit/PR público até corrigidos
* canal restrito de comunicação
* notificação responsável aos afetados
* registro em audit interno

---

# 10. RELAÇÃO COM BACKLOG

Bugs S2 e S3 podem entrar em sprints de polimento.

Bugs S0 e S1 quebram a sprint atual.

---

# 11. MÉTRICAS

Acompanhar mensalmente:

* bugs abertos por severidade
* tempo médio de resolução por severidade
* bugs reabertos
* bugs por módulo
* origem (dev, staging, prod, usuário)

---

# 12. OBJETIVO

Manter qualidade alta e transparência total sobre o que está quebrado.

Bug escondido é dívida que cresce.

Bug registrado é bug que será resolvido.
