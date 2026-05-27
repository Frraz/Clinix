# WhatsApp — Clinix

> Estratégia, arquitetura e diretrizes do módulo de WhatsApp do Clinix.

---

# 1. VISÃO GERAL

WhatsApp é parte CENTRAL do produto.

Não é integração opcional.

É camada operacional.

A maioria das clínicas regionais já opera no WhatsApp manualmente.

O Clinix automatiza isso.

---

# 2. OBJETIVOS

Garantir:

* envio automatizado de mensagens transacionais
* templates aprovados
* respostas estruturadas dos pacientes
* histórico centralizado
* atendimento humano quando necessário
* rastreabilidade total
* economia de custo operacional da clínica

---

# 3. STACK OFICIAL

* Evolution API (self-hosted)
* WhatsApp Business Cloud API (oficial)
* Redis + Celery para filas
* webhooks recebidos via endpoint dedicado

---

# 4. POR QUE EVOLUTION API

* self-hosted, sem custo por mensagem
* roda na mesma VPS
* suporta API oficial do WhatsApp Business
* APIs REST claras
* webhook estável

---

# 5. POR QUE WHATSAPP BUSINESS OFICIAL (FUTURO)

Para tenants enterprise:

* selo verificado
* templates oficiais aprovados
* maior taxa de entrega
* compliance Meta

Oferecer como add-on pago.

---

# 6. FLUXOS AUTOMATIZADOS

## Confirmação de consulta

* gatilho: 48h antes do agendamento
* mensagem com botões Confirmar / Reagendar / Cancelar
* resposta atualiza status do agendamento

---

## Lembrete

* gatilho: 24h antes
* lembrete: 2h antes
* mensagem amigável

---

## Pós-consulta

* gatilho: 2h após atendimento
* agradecimento + opcional NPS
* convite para retorno futuro

---

## Resultado de exame

* gatilho: ao liberar laudo
* link autenticado para portal do paciente
* notificação opcional ao profissional solicitante

---

## Cobrança preventiva

* D-3 do vencimento → lembrete amigável
* D+3 → primeiro aviso
* D+7 → segundo aviso
* D+15 → escalação ao financeiro

---

## Recuperação de faltoso

* gatilho: 2h após no-show
* mensagem empática
* link para reagendamento

---

## Solicitação de retorno

* gatilho: 30 / 60 / 90 dias após última consulta
* mensagem personalizada
* link de agendamento direto

---

# 7. TEMPLATES

Cada mensagem padrão deve ser:

* template versionado
* variáveis tipadas
* PT-BR
* curto
* amigável
* sem emoji excessivo
* sem caixa alta abusiva

---

# 8. CATEGORIAS DE TEMPLATES (META)

Respeitar categorização oficial:

* `UTILITY` (transacional)
* `MARKETING` (campanhas)
* `AUTHENTICATION` (OTP)

Templates de marketing exigem opt-in explícito.

---

# 9. ENVIO

Fluxo técnico:

```txt
Evento de domínio
   ↓
Serviço de mensageria
   ↓
Celery task (whatsapp.send)
   ↓
Evolution API
   ↓
WhatsApp
   ↓
Webhook de status
   ↓
Atualização do registro
```

---

# 10. RECEBIMENTO

Webhook recebe:

* nova mensagem do paciente
* confirmação de entrega
* leitura
* respostas a botões
* opt-out

---

# 11. CHAT HUMANO

Inbox unificada na clínica:

* conversas em tempo real
* atribuição a operador
* tags
* notas internas
* histórico do paciente vinculado

---

# 12. CHATBOT INICIAL

Fluxos automatizados básicos:

* triagem inicial
* agendamento auto-serviço
* status de exame
* envio de localização da clínica
* horário de funcionamento

Sempre com escape para humano.

---

# 13. HISTÓRICO

Toda mensagem deve persistir:

* tenant_id
* patient_id
* phone
* direction (in/out)
* template_name
* content
* status
* provider_message_id
* sent_at
* delivered_at
* read_at

---

# 14. STATUS DE MENSAGEM

* `queued`
* `sent`
* `delivered`
* `read`
* `failed`
* `opt_out`

---

# 15. RETRY E FILAS

* fila dedicada para WhatsApp
* retry com backoff exponencial
* dead-letter queue após N falhas
* alerta operacional se a fila acumular

---

# 16. RATE LIMITING

Respeitar:

* limites da Meta (varia por nível)
* limites da Evolution API
* throttle por tenant
* throttle por destinatário

---

# 17. OPT-OUT

Obrigatório:

* qualquer paciente pode pedir para parar de receber
* palavra-chave `SAIR` reconhecida automaticamente
* registro de opt-out por canal e finalidade
* respeito a LGPD

---

# 18. JANELA DE 24h

Respeitar regra Meta:

* após 24h sem interação, só templates aprovados
* dentro de 24h, mensagens livres permitidas

---

# 19. TEMPLATES APROVADOS

Workflow de aprovação:

* template criado no painel
* submetido à Meta
* status acompanhado
* uso bloqueado até aprovação

---

# 20. ANEXOS

Suportar envio de:

* imagens
* PDFs (laudos, receitas, recibos)
* áudios
* localização

---

# 21. PERSONALIZAÇÃO

Variáveis suportadas:

```txt
{{patient_name}}
{{appointment_date}}
{{appointment_time}}
{{professional_name}}
{{clinic_name}}
{{link}}
```

---

# 22. AUDITORIA

Toda mensagem rastreada:

* quem disparou (humano ou automação)
* por qual gatilho
* template usado
* parâmetros
* resultado

---

# 23. SEGURANÇA

* links autenticados com TTL
* nunca enviar dados clínicos no corpo da mensagem
* preferir links para o portal autenticado
* opt-out respeitado sempre

---

# 24. PERFORMANCE

* envio assíncrono sempre
* nenhum endpoint da API bloqueia esperando WhatsApp
* webhook processado rapidamente
* logs estruturados

---

# 25. MÉTRICAS

Dashboards essenciais:

* mensagens enviadas / mês
* taxa de entrega
* taxa de leitura
* taxa de resposta
* taxa de opt-out
* faltas evitadas (estimativa)
* custo por mensagem

---

# 26. CUSTO

Controlar custo:

* limites por plano (ver `billing.md`)
* alerta de uso anômalo
* opção de upgrade rápido
* cobrança de excedente

---

# 27. CAMPANHAS

Para marketing/CRM:

* segmentação de pacientes
* respeito a opt-in
* template MARKETING aprovado
* agendamento
* relatório de resultado

---

# 28. INTEGRAÇÃO COM CRM

WhatsApp alimenta:

* pipeline de leads
* funil de retorno
* timeline do paciente
* CRM da clínica

---

# 29. INTEGRAÇÃO COM AUTOMAÇÕES

Eventos WhatsApp disparam workflows:

* resposta "1" confirma consulta
* resposta "2" reagenda
* opt-out remove do funil
* "preciso falar com humano" cria ticket

---

# 30. MÚLTIPLOS NÚMEROS

Suportar por tenant:

* múltiplos números
* por unidade
* por especialidade
* por departamento

---

# 31. O QUE EVITAR

Nunca permitir:

* envio sem template em janela fechada
* envio sem opt-in para marketing
* mensagens em massa não solicitadas
* exposição de dados sensíveis no corpo
* envio sem tenant
* override de número entre tenants

---

# 32. FLUXOS POR ESPECIALIDADE

Além dos fluxos genéricos (confirmação, lembrete, pós-consulta, cobrança), cada especialidade tem fluxos próprios que entregam valor real.

## Dermatologia

* `dermato_pos_procedimento` — orientações pós-procedimento (limpeza, fotoproteção, retorno)
* `dermato_resultado_biopsia` — aviso de resultado disponível (sem expor diagnóstico no corpo)
* `dermato_seguimento` — lembrete de rastreio periódico de lesões
* `dermato_orientacao_solar` — orientação sazonal de fotoproteção

## Clínicas de Estética

* `estetica_confirmacao_sessao` — lembrete 24h antes
* `estetica_pos_sessao` — orientações pós-procedimento (evitar sol, hidratação, repouso)
* `estetica_lembrete_proxima` — próxima sessão do pacote (D-3)
* `estetica_manutencao` — sugestão de manutenção (mensal/trimestral)
* `estetica_aniversario_cliente` — fidelização

## Nutrição

* `nutri_envio_plano` — plano alimentar disponível no portal (com link)
* `nutri_lembrete_pesagem` — registrar peso semanalmente
* `nutri_motivacional` — manutenção da aderência
* `nutri_proxima_consulta` — lembrete de retorno

## Odontologia

* `odonto_proxima_sessao_plano` — próxima etapa do plano de tratamento
* `odonto_retorno_profilaxia` — lembrete semestral
* `odonto_pos_procedimento` — orientações pós-extração, pós-implante, etc.

## Fisioterapia

* `fisio_proxima_sessao` — lembrete de sessão
* `fisio_exercicios` — envio de orientações entre sessões
* `fisio_alta` — celebração da alta

## Psicologia

* `psico_lembrete_consulta` — lembrete (com sigilo no texto)
* `psico_escala_pre_consulta` — link para responder PHQ-9/GAD-7 antes da consulta

## Biomedicina / Laboratório

* `lab_resultado_disponivel` — laudo disponível no portal
* `lab_coleta_orientacoes` — orientações pré-coleta (jejum, etc.)

## Princípios transversais

* **opt-out funciona em todos os fluxos** — paciente que pediu PARAR não recebe nada
* **diagnóstico nunca no corpo da mensagem** — apenas avisos genéricos com link
* **link sempre assinado e expira em 15 min**
* **template aprovado pela Meta** quando exige
* **resposta automática só dentro da janela de 24h**

Detalhamento de templates e variáveis em arquivos `templates/` dentro do app de WhatsApp.

---

# 33. OBJETIVO FINAL

Construir uma camada WhatsApp:

* nativa
* automatizada
* compliance Meta + LGPD
* rastreável
* economicamente viável
* personalizada por especialidade sem virar caos de templates
* central na operação da clínica
* diferencial competitivo claro
