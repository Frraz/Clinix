# Auditoria — Clinix

> Estratégia de auditoria, trilhas de ação e rastreabilidade do Clinix.

---

# 1. VISÃO GERAL

O Clinix manipula dados clínicos, financeiros e pessoais altamente sensíveis.

A auditoria do sistema deve ser:

* completa
* imutável
* contextual
* multi-tenant
* compatível com LGPD
* compatível com CFM/CFO/CFP

---

# 2. OBJETIVOS

Garantir:

* rastreabilidade total
* prova legal de ações
* investigação de incidentes
* compliance médico
* defesa contra disputas
* visibilidade operacional

---

# 3. PRINCÍPIOS

Toda auditoria deve:

* registrar o que aconteceu
* registrar quem fez
* registrar quando
* registrar de onde
* registrar o que mudou
* ser imutável

---

# 4. STACK

Tooling oficial:

* `django-auditlog` para modelos críticos
* tabela `audit_logs` própria para eventos de domínio
* logs estruturados em JSON via stdout
* Sentry para erros

---

# 5. ENTIDADES AUDITADAS

## Críticas

* prontuário e evoluções
* prescrições
* exames e laudos
* documentos clínicos
* pacientes (dados sensíveis)
* permissões e papéis
* usuários
* configurações da clínica

---

## Financeiras

* cobranças
* pagamentos
* parcelamentos
* repasses
* glosas
* descontos

---

## Operacionais

* agendamentos (criação, cancelamento, reagendamento)
* check-ins
* envios de WhatsApp
* automações executadas

---

# 6. EVENTOS DE SEGURANÇA

Sempre registrar:

* login bem-sucedido
* login falho
* logout
* reset de senha
* alteração de senha
* ativação de MFA
* acesso a prontuário
* acesso a dados sensíveis
* exportação de dados
* hard delete
* alterações de RBAC

---

# 7. CAMPOS DO AUDIT LOG

Toda entrada deve conter:

```python
{
  "id": uuid,
  "tenant_id": int,
  "user_id": int,
  "actor_role": str,
  "entity_type": str,
  "entity_id": int,
  "action": str,
  "old_data": jsonb,
  "new_data": jsonb,
  "diff": jsonb,
  "context": jsonb,
  "ip_address": str,
  "user_agent": str,
  "request_id": uuid,
  "created_at": datetime
}
```

---

# 8. ACTIONS PADRÃO

Vocabulário controlado:

* `create`
* `update`
* `delete`
* `soft_delete`
* `restore`
* `view`
* `export`
* `login`
* `logout`
* `permission_change`
* `status_change`
* `release`
* `cancel`
* `sign`

---

# 9. IMUTABILIDADE

Audit logs:

* nunca podem ser editados
* nunca podem ser deletados via API
* trigger no PostgreSQL bloqueia UPDATE/DELETE
* expurgo controlado apenas via job auditável

---

# 10. RETENÇÃO

Política de retenção:

* dados clínicos: 20 anos (CFM)
* dados financeiros: 5 anos
* dados de acesso: 6 meses
* dados de segurança: 2 anos
* dados de LGPD: prazo legal

---

# 11. ACESSO AOS LOGS

Quem pode consultar:

* administrador do tenant
* DPO da clínica
* super-admin SaaS (com justificativa registrada)

---

## Nunca permitir

* acesso de operacional comum
* exposição via API pública
* acesso sem registro de quem consultou

---

# 12. CONTEXTO DE REQUEST

Todo log deve carregar:

* `request_id` (UUID por requisição)
* `tenant_id`
* `user_id`
* `ip_address`
* `user_agent`
* origem (web, mobile, automação, webhook)

---

# 13. AUDITORIA DE LEITURA

Tabelas críticas exigem log de leitura:

* `medical_records.view`
* `prescriptions.view`
* `patients.view`
* `exam_results.view`

Para compliance LGPD/CFM.

---

# 14. AUDITORIA DE EXPORTAÇÃO

Toda exportação deve registrar:

* arquivo gerado
* filtros usados
* quantidade de registros
* usuário responsável
* finalidade declarada (quando aplicável)

---

# 15. AUDITORIA DE AUTOMAÇÕES

Cada execução de workflow deve registrar:

* gatilho
* condições avaliadas
* ações executadas
* resultado
* falhas

---

# 16. AUDITORIA DE IA

Toda execução de IA deve registrar:

* prompt enviado (resumido)
* modelo utilizado
* resposta retornada
* aceitação ou rejeição pelo usuário
* tenant
* paciente envolvido

---

# 17. LOGS ESTRUTURADOS

Padrão para logs de aplicação:

```json
{
  "timestamp": "2026-05-27T10:00:00Z",
  "level": "info",
  "tenant_id": 10,
  "user_id": 55,
  "request_id": "uuid",
  "module": "appointments",
  "action": "create",
  "message": "Consulta criada",
  "metadata": { "appointment_id": 123 }
}
```

---

# 18. CORRELATION ID

Toda request gera `request_id` único.

Propagado em:

* logs
* audit logs
* Celery tasks
* webhooks
* Sentry events

Para correlacionar incidentes.

---

# 19. INVESTIGAÇÃO DE INCIDENTES

A auditoria deve permitir responder:

* quem acessou o prontuário de X paciente?
* quem alterou o valor de Y cobrança?
* quem excluiu Z documento?
* de onde veio cada ação?
* qual a sequência de eventos antes da falha?

---

# 20. UI DE AUDITORIA

Administradores devem ter painel:

* timeline de ações por entidade
* filtros por usuário, módulo, período
* visualização do diff old/new
* export controlado

---

# 21. ALERTAS DE AUDITORIA

Eventos que disparam alerta:

* acesso massivo a prontuários
* exportação fora de horário
* múltiplos logins falhos
* alteração de permissões críticas
* hard delete
* exclusão de audit logs (tentativa)

---

# 22. PROVA LEGAL

Para uso jurídico:

* logs devem ser assináveis criptograficamente
* hash encadeado opcional para tamper-evidence
* backups imutáveis no R2

---

# 23. AUDITORIA E TENANT

Logs sempre carregam `tenant_id`.

Cada tenant acessa apenas seus próprios logs.

Super-admin acessa transversalmente apenas com justificativa registrada.

---

# 24. PERFORMANCE

Audit logs em alta escala exigem:

* particionamento por mês
* índices em `tenant_id + created_at`
* índice em `entity_type + entity_id`
* arquivamento de logs antigos em storage frio

---

# 25. O QUE EVITAR

Nunca permitir:

* ações críticas sem auditoria
* logs editáveis
* logs sem tenant
* logs sem usuário
* logs sem contexto
* exposição de logs sensíveis

---

# 26. OBJETIVO FINAL

Construir uma camada de auditoria:

* completa
* imutável
* defensável juridicamente
* preparada para LGPD, CFM, CFO, CFP
* escalável
* operacionalmente útil
