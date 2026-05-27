# CRM — Clinix

> Módulo de CRM da clínica: leads, funil, retenção e relacionamento com pacientes.

---

# 1. VISÃO GERAL

CRM no Clinix tem duas dimensões:

* **CRM comercial** — captação de novos pacientes (leads → pacientes)
* **CRM de relacionamento** — retenção, recuperação e fidelização de pacientes existentes

Este doc cobre ambos.

NÃO confundir com o CRM-pelos-conselhos (registro profissional do médico).

---

# 2. OBJETIVOS

Garantir:

* nenhum lead perdido por falta de follow-up
* recuperação ativa de faltosos
* reativação de pacientes inativos
* visibilidade do funil comercial
* relacionamento contínuo com base ativa
* dados que alimentam decisões da clínica

---

# 3. PRINCÍPIOS

* lead recebe resposta rápida
* automação cobre a base, humano cobre o crítico
* segmentação respeita LGPD (opt-in para marketing)
* histórico unificado (mesmo paciente em todos os módulos)
* métricas claras de funil

---

# 4. ENTIDADES

## Principais

* `Lead` — pessoa interessada que ainda não é paciente
* `LeadSource` — origem do lead (Google, indicação, Instagram, etc.)
* `Funnel` — funil configurável
* `FunnelStage` — etapa do funil
* `Pipeline` — instância de movimentação do lead pelo funil
* `Interaction` — interação com lead/paciente
* `CrmTask` — tarefa atribuída
* `CrmSegment` — segmento de pacientes
* `Campaign` — campanha de comunicação

---

# 5. LEAD

## Origem

* formulário no site da clínica
* WhatsApp inicial
* indicação
* Google Ads
* Instagram
* Doctoralia
* telefone
* presencial

---

## Dados captados

* nome
* contato (WhatsApp, email)
* especialidade de interesse
* origem
* observações
* consentimento LGPD

---

## Não confundir com paciente

* lead vira `Patient` somente quando agenda primeira consulta
* histórico do lead preservado mesmo após conversão

---

# 6. FUNIL COMERCIAL

## Etapas típicas

```txt
novo lead
   ↓
contato realizado
   ↓
interessado
   ↓
agendamento marcado
   ↓
paciente atendido (conversão)
   ↓
retornou
```

---

## Configurável

Cada clínica configura próprio funil:

* nomes de etapas
* cores
* SLA por etapa
* automações por etapa

---

# 7. PIPELINE VISUAL

Kanban no painel:

* colunas = etapas
* cards = leads
* drag-and-drop entre etapas
* filtros por origem, profissional, especialidade
* busca rápida

---

# 8. ATRIBUIÇÃO

Leads podem ser atribuídos:

* automaticamente (round-robin)
* manualmente (gestor decide)
* por origem (Instagram → atendente Y)
* por especialidade

Responsável recebe notificação interna.

---

# 9. SLA DE LEAD

Tempo máximo para primeira resposta:

* < 5 minutos (ideal)
* < 30 minutos (aceitável)
* > 1 hora (alerta vermelho)

Dashboard mostra leads com SLA estourado.

---

# 10. AUTOMAÇÕES NO FUNIL

Eventos disparam workflows:

* lead criado → WhatsApp automático de boas-vindas
* lead em "interessado" por 3 dias → lembrete ao responsável
* lead em "agendamento marcado" → confirma 24h antes
* lead virou paciente → onboarding do paciente
* lead perdido → tag para retomar em 30 dias

Ver `docs/workflows.md`.

---

# 11. INTERAÇÕES

Toda interação registrada:

* WhatsApp enviado/recebido
* email enviado/aberto/clicado
* ligação realizada
* visita presencial
* observação manual

Timeline unificada por lead/paciente.

---

# 12. TAREFAS

Sistema interno de tarefas:

* "ligar para João até hoje 17h"
* "enviar orçamento para Maria"
* "reativar pacientes inativos há 90 dias"

Com:

* prioridade
* prazo
* responsável
* status

---

# 13. SEGMENTAÇÃO DE PACIENTES

Critérios:

* por especialidade
* por idade
* por última consulta
* por valor gasto
* por convênio
* por tags customizadas
* por procedimento realizado
* por NPS

Filtros combináveis. Salvos como segmento reutilizável.

---

# 14. CAMPANHAS

Disparo segmentado:

* WhatsApp (com opt-in)
* email
* combinação

Fluxo:

* seleciona segmento
* escolhe template
* agenda envio
* preview
* aprovação
* envio
* relatório de resultado

---

# 15. FUNIS PRÉ-PRONTOS

Templates oferecidos pelo Clinix:

* **Captação** — lead → primeira consulta
* **Recuperação de faltoso** — no-show → reagendamento
* **Reativação** — inativo 90 dias → retorno
* **Pós-tratamento** — alta → retorno em X meses
* **NPS** — pós-consulta → coleta + ação

Cada um com workflows associados.

---

# 16. RETENÇÃO

Métricas-chave:

* taxa de retorno em 30/60/90 dias
* tempo médio entre consultas
* % de pacientes ativos
* churn de pacientes

Automações de retenção:

* X dias sem consulta → mensagem
* Y meses sem consulta → segunda mensagem
* Z meses sem consulta → marca como inativo

---

# 17. NPS

Coleta automatizada:

* 7 dias após consulta
* WhatsApp pergunta nota 0-10
* nota < 7 → cria tarefa interna (gestor analisa)
* nota 7-8 → registra
* nota 9-10 → convida para Google Reviews

Dashboard mostra:

* NPS médio
* evolução
* por profissional
* por especialidade
* feedbacks textuais

---

# 18. ANIVERSÁRIO

Toque humano:

* envio de mensagem (opt-in)
* template personalizável
* horário configurável (8h da manhã default)
* desligável globalmente

---

# 19. INDICAÇÃO

Programa de indicação:

* paciente indica amigo
* sistema rastreia origem
* gestor pode oferecer benefício (configurável)
* relatório de top indicadores

---

# 20. DASHBOARD DE CRM

KPIs essenciais:

* leads no mês
* taxa de conversão por origem
* tempo médio até primeira resposta
* funil completo (visualização)
* campanhas em andamento
* NPS atual
* % de pacientes ativos
* leads perdidos

---

# 21. RELATÓRIOS

Periódicos:

* funil mensal
* ROI de campanhas
* origem de novos pacientes
* recuperação de faltosos
* reativação de inativos
* NPS evolutivo

---

# 22. INTEGRAÇÃO COM AGENDA

* agendamento marca lead como convertido
* no-show alimenta funil de recuperação
* conclusão de consulta alimenta funil pós-atendimento

---

# 23. INTEGRAÇÃO COM PRONTUÁRIO

CRM NÃO acessa prontuário sem permissão.

Mas alimenta:

* timeline do paciente com interações comerciais
* tags para segmentação
* histórico de comunicação

---

# 24. INTEGRAÇÃO COM FINANCEIRO

* LTV (lifetime value) por paciente
* receita por origem de lead
* ROI de campanhas
* CAC por origem

---

# 25. INTEGRAÇÃO COM WHATSAPP

* mensagens do funil via WhatsApp
* histórico unificado
* opt-out respeitado

Ver `docs/whatsapp.md`.

---

# 26. PERMISSÕES

| Ação | Quem |
|------|------|
| Criar lead | recepcionista, gestor |
| Mover no funil | atribuído ou gestor |
| Configurar funil | admin, gestor |
| Disparar campanha | gestor |
| Ver relatórios | gestor, financeiro |
| Configurar automações | admin, gestor |

---

# 27. AUDITORIA

Registrar:

* criação/edição de lead
* mudanças no funil
* campanhas disparadas
* opt-out de paciente
* segmentação usada

---

# 28. LGPD NO CRM

Crítico:

* opt-in explícito para marketing
* base legal registrada
* descadastro fácil
* dados de lead seguem mesmas regras de paciente
* anonimização de leads inativos após prazo

Ver `docs/lgpd.md`.

---

# 29. PERFORMANCE

* funil renderiza com cache invalidado por evento
* dashboards com refresh inteligente
* segmentação calculada assincronamente para volumes grandes
* envio de campanha em workers dedicados

---

# 30. ROADMAP

## Fase 1 (MVP)

* não incluído

---

## Fase 2

* cadastro de lead
* funil básico
* atribuição manual
* automações básicas (WhatsApp de boas-vindas)
* NPS automático
* dashboard simples

---

## Fase 2.5

* funis configuráveis
* campanhas segmentadas
* programa de indicação
* dashboards completos
* automações de retenção
* tarefas internas

---

## Fase 3

* IA prediz risco de churn
* IA sugere segmentos
* IA personaliza mensagens
* análise preditiva de funil

---

# 31. O QUE EVITAR

Nunca permitir:

* campanha sem opt-in
* lead duplicado silencioso
* funil cross-tenant
* opt-out ignorado
* tarefa sem responsável
* segmento com PII exposta indevidamente

---

# 32. OBJETIVO FINAL

Construir um CRM que:

* nenhuma clínica usa marketing à mão depois
* leads não são perdidos
* pacientes retornam mais
* gestor enxerga origem da receita
* respeita LGPD rigorosamente
* é diferencial competitivo
