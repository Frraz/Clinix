# Importação e Exportação de Dados — Clinix

> Estratégia oficial de portabilidade de dados no Clinix: entrada de clientes vindos de outros sistemas, e saída de clientes que deixam o produto.

---

# 1. VISÃO GERAL

Portabilidade de dados é tema estruturante.

Afeta:

* onboarding (entrada de clínicas vindas de outros sistemas)
* offboarding (saída de clínicas)
* LGPD (direito à portabilidade — art. 18, V)
* migração entre planos
* backups exportáveis
* uso operacional (export de relatórios, listas, agendas)

Decisão estratégica:

> O Clinix NUNCA aprisiona o cliente.

Portabilidade total é diferencial competitivo e exigência legal.

---

# 2. OBJETIVOS

Garantir:

* onboarding rápido para clínicas vindas de outros sistemas
* exportação completa, estruturada e legível ao sair
* respeito ao art. 18 da LGPD
* prevenção de vendor lock-in (transparência como argumento de venda)
* uso operacional diário (export de listagens, relatórios, agendas)

---

# 3. PRINCÍPIOS

Toda portabilidade deve:

* preservar integridade dos dados
* respeitar tenant
* respeitar LGPD
* gerar auditoria
* ser executável sem suporte técnico do Clinix
* ter formato aberto e documentado

---

# 4. CATEGORIAS DE PORTABILIDADE

## Importação

Trazer dados de fora para o Clinix.

* clínicas migrando de outros sistemas
* upload de listas operacionais
* import recorrente via API/integração

---

## Exportação operacional

Uso cotidiano.

* relatórios em PDF
* listagens em CSV/Excel
* agendas em iCal
* prontuário individual em PDF

---

## Exportação completa (offboarding)

Saída total da plataforma.

* clínica cancela contrato
* paciente solicita portabilidade (LGPD)
* gestor solicita auditoria externa

---

# 5. IMPORTAÇÃO — VISÃO GERAL

Fontes típicas de origem dos novos clientes:

* iClinic
* Doctoralia
* Amplimed
* Conexa
* Ninsaúde
* MV Sistemas
* Tasy
* planilhas Excel/Google Sheets
* WhatsApp + papel + caderno (caso muito comum)
* sistemas próprios antigos

---

# 6. FORMATOS DE IMPORTAÇÃO ACEITOS

## MVP

* CSV (UTF-8, separador `;`)
* Excel (.xlsx)

---

## Fase 2

* JSON estruturado
* API REST de import
* webhooks de import contínuo

---

## Fase 2.5

* import direto de sistemas concorrentes via export padrão
* parser dedicado para os top 5 sistemas do mercado

---

# 7. ENTIDADES IMPORTÁVEIS

## Sempre

* pacientes
* profissionais
* especialidades
* convênios
* tipos de consulta
* agenda histórica
* contas a receber em aberto
* documentos do paciente
* histórico clínico textual

---

## Quando viável

* prontuários estruturados (depende do formato de origem)
* exames
* prescrições
* anexos (PDFs, imagens)
* histórico financeiro consolidado

---

# 8. PIPELINE DE IMPORTAÇÃO

```txt
Upload do arquivo
   ↓
Validação de formato
   ↓
Mapeamento de colunas (UI)
   ↓
Preview com primeiras 10 linhas
   ↓
Validação por linha
   ↓
Relatório de erros (download CSV)
   ↓
Aprovação humana
   ↓
Celery task assíncrona
   ↓
Import idempotente (chave de deduplicação)
   ↓
Audit log completo
   ↓
Relatório final
```

---

# 9. MAPEAMENTO DE COLUNAS

Interface visual permite:

* mapear coluna do arquivo → campo do Clinix
* salvar template de mapeamento (reutilizável)
* aplicar transformações simples (trim, lowercase, formato de data)
* visualizar preview antes de importar

---

# 10. VALIDAÇÃO

Cada linha passa por:

* validação de tipo
* validação de formato (CPF, CEP, email, telefone)
* validação de obrigatoriedade
* detecção de duplicidade
* normalização (acentos, espaços)

---

# 11. ESTRATÉGIA DE DEDUPLICAÇÃO

## Pacientes

Chave de deduplicação (em ordem):

1. CPF (quando presente e válido)
2. data de nascimento + nome normalizado
3. telefone principal

Ao detectar duplicata:

* `merge` (combinar campos preenchidos)
* `skip` (ignorar)
* `overwrite` (substituir)
* `create_anyway` (criar como novo)

Decidido pelo usuário antes do import.

---

## Agendamentos

Chave:

* paciente + profissional + data/hora

Duplicatas: skip por padrão.

---

# 12. ERROS DE IMPORTAÇÃO

Saída obrigatória:

* CSV com linhas que falharam
* coluna de erro descritivo
* link para correção
* opção de reimportar apenas as falhas

---

# 13. IMPORT INCREMENTAL

Após o import inicial, suportar:

* import de delta (apenas registros novos)
* sync recorrente via API
* webhook do sistema origem (quando disponível)

---

# 14. IMPORT VIA API

Endpoint dedicado:

```
POST /api/v1/imports/start
{
  "entity": "patients",
  "file_url": "...",
  "mapping_template_id": "...",
  "options": {
    "deduplication": "merge",
    "dry_run": false
  }
}
```

Retorna `job_id` para acompanhamento assíncrono.

---

# 15. IMPORT DE ANEXOS

Para PDFs, imagens, exames:

* arquivo ZIP estruturado por paciente
* CSV de metadata associado
* upload progressivo (chunked)
* validação de tipo + tamanho
* armazenamento no R2 com `tenant_id` no path

---

# 16. AUDITORIA DE IMPORTAÇÃO

Registrar:

* quem importou
* quando
* qual arquivo (hash + original_filename)
* quantos registros criados / atualizados / falhados
* mapeamento usado
* opções aplicadas

Retenção mínima: 5 anos.

---

# 17. EXPORTAÇÃO OPERACIONAL

Usos cotidianos:

* listagem de pacientes em CSV
* lista de agenda do dia em PDF
* relatório financeiro mensal em PDF/Excel
* histórico de paciente em PDF (para entregar ao paciente)
* receitas e atestados em PDF
* laudos em PDF
* timeline clínica em PDF

---

# 18. PADRÕES DE EXPORT

| Formato | Uso |
|---------|-----|
| CSV (UTF-8 BOM) | listagens tabulares, compatível com Excel BR |
| Excel (.xlsx) | relatórios estruturados |
| PDF | documentos legíveis, assinados, com branding |
| JSON | export estruturado para integração |
| iCal | agendas |
| ZIP | export completo (multi-arquivo) |

---

# 19. EXPORT ASSÍNCRONO

Exports grandes são sempre assíncronos:

```
POST /api/v1/exports/start
   → retorna job_id

GET /api/v1/exports/:job_id
   → status: queued | processing | done | failed
   → download_url (com TTL)
```

Nunca bloqueia a request.

---

# 20. EXPORT COMPLETO (OFFBOARDING)

Direito garantido a toda clínica.

Disponível a qualquer momento, mesmo fora de cancelamento.

---

## O que está incluído

* pacientes (todos os dados)
* prontuários completos
* prescrições
* exames e laudos
* documentos e anexos
* agenda histórica
* dados financeiros
* dados de WhatsApp (mensagens, templates)
* configurações da clínica
* logs de auditoria

---

## Formatos do export completo

* `data/` — JSON estruturado por entidade
* `patients/` — pasta por paciente com PDFs do prontuário
* `documents/` — anexos originais
* `audit/` — logs em CSV
* `README.txt` — instruções e dicionário de dados
* `schema.json` — schema completo do export

Tudo empacotado em ZIP criptografado com senha entregue ao admin.

---

# 21. PROCESSO DE OFFBOARDING

```txt
Cancelamento solicitado
   ↓
Janela de 30 dias de operação somente leitura
   ↓
Solicitação de export completo gerada automaticamente
   ↓
Email + WhatsApp ao admin com instruções
   ↓
Job assíncrono gera o ZIP
   ↓
Notificação ao admin com link (TTL 30 dias)
   ↓
+60 dias: dados anonimizados (dados clínicos preservados por obrigação CFM)
   ↓
+90 dias: tenant arquivado
```

---

# 22. EXPORT POR PACIENTE (LGPD)

Direito do titular — art. 18, V.

Disponível no portal do paciente:

* botão "Solicitar meus dados"
* job assíncrono
* prazo máximo: 15 dias
* notificação ao concluir
* link com TTL de 7 dias
* registro em audit + RIPD

---

# 23. SLA DE PORTABILIDADE

| Operação | SLA |
|----------|-----|
| Export operacional pequeno | < 30s |
| Export operacional grande | < 5min |
| Export completo de tenant | < 24h |
| Export LGPD do paciente | < 15 dias |
| Import de < 1.000 registros | < 5min |
| Import de < 100.000 registros | < 1h |

---

# 24. SEGURANÇA

* URLs de download sempre assinadas, com TTL
* arquivos criptografados (AES-256 em repouso no R2)
* export completo entregue com senha forte gerada
* nunca expor link público sem autenticação
* audit log de cada download
* alerta a admin se múltiplos exports em curto período

---

# 25. PERMISSÕES

| Ação | Quem |
|------|------|
| Importar dados | admin, gestor |
| Export operacional simples | conforme RBAC do módulo |
| Export por paciente individual | profissional do tenant |
| Export completo do tenant | apenas admin |
| Export LGPD do paciente | paciente (via portal) |
| Aprovar mapeamento de import | admin |

---

# 26. ONBOARDING ASSISTIDO

Para clínicas vindas de sistemas concorrentes:

* questionário inicial (origem dos dados, volume)
* download de template pré-configurado
* parser dedicado para top 5 sistemas (Fase 2)
* suporte humano opcional (cobrado ou incluso por plano)
* validação assistida do mapeamento

---

# 27. DOCUMENTAÇÃO PARA O CLIENTE

Disponibilizar publicamente:

* dicionário de dados de cada export
* schema JSON versionado
* tutorial passo a passo de import
* exemplo de arquivo CSV preenchido
* FAQ
* canal de suporte dedicado para migrações

---

# 28. VERSIONAMENTO DE SCHEMA

Schemas de import/export são versionados:

* `v1.0.0`, `v1.1.0` etc.
* backward compatibility por pelo menos 12 meses
* depreciação anunciada com 6 meses de antecedência

---

# 29. PERFORMANCE

Imports e exports grandes:

* processados em workers dedicados
* não impactam performance do tenant em produção
* throttling por tenant
* prioridade configurável (Enterprise > Business > Professional)

---

# 30. INTEGRAÇÃO COM CRM/MARKETING

Argumento de venda explícito:

> "Você pode sair quando quiser. Levamos todos os seus dados em um clique."

Documentação pública reforça transparência.

---

# 31. INTEGRAÇÃO COM LGPD

Cross-references:

* portabilidade do titular → ver `docs/lgpd.md`
* anonimização pós-saída → ver `docs/lgpd.md`
* audit obrigatório → ver `docs/auditoria.md`

---

# 32. INTEGRAÇÃO COM BILLING

Cross-references:

* janela de 30 dias após cancelamento → ver `docs/billing.md`
* retenção de dados pós-cancelamento → ver `docs/billing.md`

---

# 33. O QUE EVITAR

Nunca permitir:

* import sem audit
* import sem `tenant_id`
* import que sobrescreve sem confirmação
* export sem autenticação
* export sem TTL
* aprisionamento (vendor lock-in)
* schema fechado/proprietário
* atrasos arbitrários em pedidos de portabilidade

---

# 34. ROADMAP

## Fase 1 (MVP)

* export operacional básico (CSV, PDF) por módulo
* import CSV de pacientes
* export completo manual (sob solicitação)

---

## Fase 1.5

* templates de mapeamento salvos
* import de agenda histórica
* export por paciente em PDF

---

## Fase 2

* parsers dedicados para iClinic, Doctoralia, Ninsaúde
* import via API
* sync incremental
* export agendado recorrente
* export completo self-service (botão no painel)

---

## Fase 3

* migração assistida por IA (mapeamento automático sugerido)
* validação semântica de dados clínicos
* import de prontuário não-estruturado com extração de campos

---

# 35. OBJETIVO FINAL

Construir uma camada de portabilidade que:

* destrava onboarding rápido para clínicas vindas de outros sistemas
* respeita rigorosamente o direito de saída
* é argumento de venda (transparência)
* atende LGPD com sobra
* nunca aprisiona o cliente
* trata dados clínicos com o cuidado exigido pelo CFM
