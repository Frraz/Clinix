# Backup e Disaster Recovery — Clinix

> Estratégia formal de backup, continuidade de negócio e recuperação de desastres do Clinix.

---

# 1. VISÃO GERAL

O Clinix manipula dados clínicos e financeiros críticos.

Perda de dados é catastrófica:

* legal (CFM exige 20 anos de prontuário)
* operacional (clínicas dependem do sistema)
* reputacional (recuperação de imagem é cara)

Este doc define:

* backup
* retenção
* RTO/RPO
* disaster recovery plan
* runbook de incidentes

---

# 2. OBJETIVOS

Garantir:

* nenhum dado clínico perdido
* recuperação rápida em caso de falha
* prova de conformidade (LGPD, CFM)
* continuidade do negócio das clínicas
* testes regulares de recuperação

---

# 3. PRINCÍPIOS

* backup automatizado, nunca manual
* backups testados regularmente
* múltiplas cópias (regra 3-2-1)
* criptografia em todos os backups
* runbook documentado
* responsável definido

---

# 4. REGRA 3-2-1

Toda dado crítico deve ter:

* **3** cópias dos dados
* em **2** mídias/serviços diferentes
* com **1** cópia off-site

No Clinix:

* primária: PostgreSQL na VPS
* secundária: backup diário no Cloudflare R2
* terciária: backup semanal em provedor independente (ex: Backblaze B2)

---

# 5. RTO E RPO

Definições:

* **RTO (Recovery Time Objective):** tempo máximo aceitável para restaurar o serviço
* **RPO (Recovery Point Objective):** quantidade máxima aceitável de dados perdidos

---

## Metas oficiais

| Métrica | Meta MVP | Meta Enterprise |
|---------|----------|-----------------|
| RTO | 4 horas | 1 hora |
| RPO | 24 horas | 15 minutos |
| Disponibilidade | 99.5% | 99.9% |

---

# 6. BACKUP DO POSTGRESQL

## Diário (full)

* `pg_dump` comprimido (gzip)
* executado às 3h (horário com menor uso)
* upload para Cloudflare R2 em path por data
* retenção: 30 dias
* verificação de integridade (`pg_restore --list`)

---

## Semanal (full + arquivamento)

* mesmo dump
* upload para provedor independente (Backblaze B2)
* retenção: 12 meses
* relatório enviado por email ao admin do Clinix

---

## Mensal (long-term)

* upload para storage frio (R2 Glacier-style)
* retenção: 7 anos
* atende exigência CFM (20 anos para prontuário, mas com anonimização possível após 5 anos)

---

## Por tenant

Quando a clínica solicitar (offboarding ou conformidade interna):

* export completo do tenant (ZIP)
* ver `docs/importacao-exportacao.md`

---

## Contínuo (WAL — Fase 2)

Para reduzir RPO:

* `archive_mode=on` no Postgres
* envio contínuo de WAL para R2
* point-in-time recovery (PITR) com granularidade de minutos

Reduz RPO de 24h para minutos.

---

# 7. BACKUP DO STORAGE (R2)

R2 já tem durabilidade alta (eleven nines).

Mas garantir:

* versionamento ativo (recuperar arquivo deletado)
* replicação cross-region (Fase 2)
* backup semanal de paths críticos para B2

---

# 8. BACKUP DO REDIS

Redis é volátil por design:

* dados em cache: NÃO backup
* dados em fila Celery: tolerante a perda (retry)
* dados de sessão: tolerante a perda (re-login)

Mas:

* AOF habilitado para persistência mínima
* dump diário do Redis (`redis-cli BGSAVE`) por precaução

---

# 9. BACKUP DE CONFIGURAÇÃO

Não é só dado. Também:

* `.env` files (criptografados, em cofre — Bitwarden/1Password)
* nginx configs (versionados em git)
* docker-compose (versionado em git)
* scripts de deploy (versionados em git)
* certificados SSL (renovação automática)
* templates de workflow customizados (no banco, já incluso)

---

# 10. CRIPTOGRAFIA

Todos os backups:

* criptografados em repouso (AES-256)
* chaves rotacionadas anualmente
* chaves armazenadas separadamente dos dados
* nunca chaves no mesmo repositório

---

# 11. RUNBOOK DE RESTAURAÇÃO

## Cenário 1 — Corrupção de tabela única

```bash
# 1. Identificar último backup íntegro
ls -la /backups/postgres/

# 2. Restaurar tabela específica
pg_restore -d clinix_prod -t medical_records backup.sql

# 3. Validar
SELECT count(*) FROM medical_records WHERE tenant_id = X;

# 4. Notificar tenant afetado
```

---

## Cenário 2 — Perda completa do banco

```bash
# 1. Provisionar nova instância Postgres
# 2. Restaurar último backup diário
pg_restore -d clinix_prod /backups/latest.sql.gz

# 3. Aplicar WALs subsequentes (se PITR ativo)
# 4. Validar integridade
# 5. Apontar aplicação para nova instância
# 6. Notificar tenants do incidente
```

---

## Cenário 3 — Perda da VPS inteira

```bash
# 1. Provisionar nova VPS
# 2. Restaurar configurações do cofre
# 3. Subir docker-compose
# 4. Restaurar Postgres (diário)
# 5. Restaurar paths críticos do R2 (já está no R2)
# 6. Apontar DNS para nova VPS
# 7. Validar healthchecks
# 8. Comunicar tenants
```

---

## Cenário 4 — Comprometimento de segurança

```bash
# 1. Isolar instância afetada
# 2. Snapshot forense
# 3. Restaurar de backup pré-incidente
# 4. Rotacionar todos os secrets
# 5. Invalidar todas as sessões
# 6. Forçar reset de senhas
# 7. Comunicar ANPD em 2 dias úteis se aplicável
# 8. Post-mortem público interno
```

Ver `docs/lgpd.md`.

---

# 12. TESTES DE RECUPERAÇÃO

Obrigatórios:

* mensal: restaurar backup em ambiente isolado
* trimestral: simulação de disaster recovery completo
* anual: auditoria externa

Resultado de cada teste documentado.

---

# 13. INCIDENTES DE INDISPONIBILIDADE

## Classificação

* P0 — sistema fora completo
* P1 — módulo crítico fora (agenda, prontuário, auth)
* P2 — módulo secundário fora
* P3 — degradação parcial

---

## Resposta P0

* notificação automática ao on-call
* status page atualizado em < 5min
* tentativa imediata de auto-recovery (restart)
* escalação se não resolver em 15min
* comunicação ao cliente em < 30min
* atualização a cada 30min até resolver

---

# 14. STATUS PAGE

Página pública:

* `status.clinix.com.br`
* incidentes em andamento
* histórico de incidentes
* uptime histórico
* manutenções programadas

---

# 15. MANUTENÇÕES PROGRAMADAS

Quando necessário:

* aviso aos clientes com 7 dias de antecedência
* janela em horário de baixo uso (3h-5h domingo)
* duração máxima planejada
* status page atualizado
* opção de adiar para clientes Enterprise

---

# 16. RESPONSABILIDADES

## Time Clinix

* manter backups funcionando
* testar recuperação
* responder a incidentes
* comunicar clientes
* registrar post-mortems

---

## Cliente (clínica)

* manter contato atualizado para incidentes
* solicitar exports periódicos próprios (se quiser cópia local)
* respeitar janelas de manutenção

---

# 17. POST-MORTEM

Todo incidente P0/P1 gera:

* timeline detalhada
* causa raiz
* impacto
* ações de mitigação tomadas
* ações de prevenção
* responsáveis
* publicação interna

Sem culpabilizar pessoas. Foco em sistema.

---

# 18. MONITORAMENTO

Alertas automáticos disparam runbook:

* DB inacessível
* fila Celery acumulada
* erro 5xx > 1% por 5min
* uso de disco > 85%
* uso de memória > 90%
* uso de CPU > 90% sustentado
* backup falhou

Stack: Sentry + Prometheus (Fase 2).

---

# 19. COMUNICAÇÃO DURANTE INCIDENTE

Canais:

* status page (principal)
* email para admins das clínicas
* WhatsApp para incidentes P0 (admin via Evolution API)
* dashboard interno do tenant com banner

Linguagem:

* honesta
* sem jargão
* foco em quando voltará
* sem culpar terceiros gratuitamente

---

# 20. DEPENDÊNCIAS EXTERNAS

Riscos identificados:

| Dependência | Risco | Mitigação |
|-------------|-------|-----------|
| Hostinger VPS | indisponibilidade | runbook de migração |
| Cloudflare R2 | indisponibilidade | backup paralelo em B2 |
| Cloudflare DNS | indisponibilidade | secundário em outro provedor |
| Evolution API | falha | retry + fallback SMS |
| Mercado Pago | falha | retry + relatório |
| Resend | falha | fallback para SES |
| OpenAI/Claude | falha | feature de IA degrada graciosamente |

---

# 21. BUSINESS CONTINUITY

Plano:

* documentação técnica acessível ao time
* runbooks atualizados
* contatos de provedores documentados
* cofre de secrets compartilhado entre 2+ pessoas
* plano de sucessão técnico

---

# 22. AUDITORIA

Toda restauração registrada:

* quem executou
* quando
* qual backup usado
* validação realizada
* tenants afetados
* comunicação enviada

---

# 23. CUSTOS

Estimativa mensal de backup:

* R2 storage diário: ~$5
* B2 storage semanal: ~$3
* tráfego de upload: incluído (R2)
* total: < R$50/mês no MVP

Escala linearmente com volume.

---

# 24. ROADMAP

## Fase 1 (MVP)

* `pg_dump` diário automatizado
* upload para R2
* versionamento R2 ativo
* runbook documentado
* status page básica

---

## Fase 1.5

* backup semanal em B2 (provedor independente)
* teste mensal de restauração
* alertas básicos

---

## Fase 2

* PITR via WAL contínuo
* simulação trimestral de DR
* status page público completo
* runbooks expandidos
* on-call rotativo

---

## Fase 6 (Enterprise)

* multi-region replication
* RTO 1h / RPO 15min
* SLA contratual de uptime
* backup dedicado por cliente Enterprise

---

# 25. O QUE EVITAR

Nunca permitir:

* backup sem teste de restauração
* secrets no mesmo lugar que dados
* dependência de um único provedor
* runbook desatualizado
* incidente sem post-mortem
* esconder incidente do cliente

---

# 26. OBJETIVO FINAL

Construir uma camada de backup e DR que:

* atende CFM (retenção 20 anos)
* atende LGPD (controle e prova)
* atende clínicas (continuidade operacional)
* atende time interno (sono tranquilo)
* permite crescer sem reescrita
