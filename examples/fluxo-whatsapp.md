# Fluxo WhatsApp — Clinix

> Jornada completa de envio, recebimento e automação de mensagens via WhatsApp.

---

# 1. ESCOPO

Cobre todos os pontos em que o WhatsApp interage com o sistema:

* envio transacional
* envio em massa (com opt-in)
* recebimento e chat humano
* automações reativas
* opt-out

Para arquitetura geral ver `docs/whatsapp.md`.

---

# 2. PERSONAS

* paciente
* recepcionista (chat humano)
* sistema (automações)
* gestor (campanhas)

---

# 3. PRÉ-CONDIÇÕES

* tenant com integração WhatsApp ativa
* templates aprovados na Meta
* franquia mensal dentro do limite
* paciente com telefone válido
* opt-in para mensagens de marketing (quando aplicável)

---

# 4. ARQUITETURA DE ENVIO

```txt
Evento de domínio (ex: appointment.created)
   ↓
Workflow avalia trigger + condições
   ↓
Serviço de mensageria monta payload
   ↓
Celery task `whatsapp.send`
   ↓
Evolution API
   ↓
WhatsApp
   ↓
Webhook de status
   ↓
Atualização do `whatsapp_messages`
```

---

# 5. CASO 1 — CONFIRMAÇÃO DE CONSULTA

## Disparo

Workflow ativado por `appointment.created`:

* delay até 48h antes do agendamento
* mensagem com template `confirmacao_consulta`
* botões: Confirmar / Reagendar / Cancelar

---

## Mensagem (template)

```
Olá {{patient_name}}!

Sua consulta com {{professional_name}} está marcada para {{appointment_date}} às {{appointment_time}}.

Por favor, confirme sua presença:
[ Confirmar ]  [ Reagendar ]  [ Cancelar ]
```

---

## Resposta do paciente

* botão Confirmar → status `confirmed`, evento `appointment.confirmed`
* botão Reagendar → fluxo de reagendamento (próximos horários)
* botão Cancelar → status `cancelled`, libera horário

---

# 6. CASO 2 — LEMBRETE DE CONSULTA

## Disparo

`appointment.confirmed`:

* 24h antes: lembrete textual
* 2h antes: lembrete curto

---

## Sem botões

Apenas informativo nesta etapa.

---

# 7. CASO 3 — RESULTADO DE EXAME

## Disparo

`exam.released`:

* link autenticado para portal do paciente
* TTL de 7 dias
* nunca envia conteúdo clínico no corpo

---

## Mensagem

```
{{patient_name}}, seu exame está disponível!

Acesse com segurança:
{{secure_link}}

Esse link expira em 7 dias.
```

---

# 8. CASO 4 — COBRANÇA PREVENTIVA

## Disparo

`invoice.created` → D-3 do vencimento.

---

## Mensagem amigável

```
Oi {{patient_name}}! 

Lembrete: sua fatura de {{amount}} vence em {{due_date}}.

Pagar agora (PIX): {{payment_link}}

Qualquer dúvida estamos aqui.
```

---

# 9. CASO 5 — RECUPERAÇÃO DE FALTOSO

## Disparo

`appointment.no_show` + delay de 2h.

---

## Mensagem empática

```
{{patient_name}}, sentimos sua falta hoje!

Quer reagendar? É rápido:
{{reschedule_link}}
```

---

# 10. CASO 6 — NPS PÓS-CONSULTA

## Disparo

`appointment.completed` + delay de 7 dias.

---

## Pergunta

```
{{patient_name}}, como foi sua experiência com {{professional_name}}?

De 0 a 10, qual a chance de você nos recomendar?

[ 0 ] [ 1 ] ... [ 10 ]
```

---

## Tratamento

* resposta < 7 → cria ticket interno
* resposta 7-8 → registra
* resposta 9-10 → convida para deixar avaliação no Google

---

# 11. CASO 7 — CHAT HUMANO

## Recebimento

Webhook recebe mensagem do paciente fora de fluxo automático.

---

## Roteamento

* identifica paciente pelo número
* identifica tenant
* abre conversa na inbox da clínica
* atribui a operador disponível (round-robin ou manual)
* notifica operador

---

## Janela de 24h

Dentro de 24h após a última mensagem do paciente:

* mensagens livres permitidas

Após 24h:

* apenas templates aprovados

---

# 12. CASO 8 — CHATBOT INICIAL

Para clínicas que ativarem:

* paciente envia mensagem inicial
* bot apresenta menu:
  * 1 → agendar
  * 2 → status de exame
  * 3 → horário de funcionamento
  * 4 → falar com atendente

---

## Escape para humano

Sempre disponível.

Palavra-chave "atendente" ou "humano" escala.

---

# 13. CASO 9 — CAMPANHA DE MARKETING

## Requisitos

* template categoria MARKETING aprovado
* opt-in do paciente
* segmentação definida

---

## Fluxo

* gestor define segmento (ex: pacientes sem consulta há 90 dias)
* preview do envio
* aprovação
* agendamento ou envio imediato
* respeito a quota mensal
* relatório de resultado

---

# 14. OPT-OUT

## Reconhecimento

Palavras-chave: `SAIR`, `PARAR`, `CANCELAR INSCRICAO`.

---

## Tratamento

* opt-out registrado por canal e finalidade
* paciente não recebe mais MARKETING
* mensagens TRANSACIONAIS críticas (resultado de exame, cobrança) podem continuar
* respeito a LGPD

---

# 15. STATUS DE MENSAGEM

* `queued` — na fila do Celery
* `sent` — enviado à Evolution API
* `delivered` — entregue ao WhatsApp do paciente
* `read` — lido pelo paciente
* `failed` — falha após retries

Atualização via webhook.

---

# 16. RETRY

* 5 tentativas com backoff exponencial
* dead-letter queue após esgotamento
* alerta operacional ao tenant

---

# 17. PERMISSÕES

| Ação | Quem |
|------|------|
| Configurar templates | admin |
| Criar workflow | admin, gestor |
| Chat humano | recepcionista, atendimento |
| Disparar campanha | gestor |
| Ver histórico | quem tem acesso ao paciente |

---

# 18. AUDITORIA

Registrar:

* template usado
* parâmetros
* origem (automação, chat humano, campanha)
* status final
* opt-out do paciente
* alterações em templates

---

# 19. MÉTRICAS

KPIs no dashboard:

* mensagens enviadas (mês)
* taxa de entrega
* taxa de leitura
* taxa de resposta
* taxa de opt-out
* faltas evitadas (estimativa)
* custo por mensagem

---

# 20. CONTROLE DE CUSTO

* alerta ao atingir 80% da franquia
* sugestão de upgrade ao atingir 100%
* bloqueio de envio não essencial quando excedido (configurável)

---

# 21. SEGURANÇA

* nunca enviar dados sensíveis no corpo
* preferir links autenticados com TTL
* opt-out respeitado sempre
* nunca cruzar dados entre tenants
* nunca expor número do paciente em logs públicos

---

# 22. O QUE EVITAR

* envio em massa sem opt-in
* envio fora da janela de 24h sem template
* mensagens em caixa alta abusiva
* excesso de emojis
* envio sem auditoria
* link sem expiração

---

# 23. OBJETIVO

WhatsApp como camada operacional que:

* automatiza confirmações e lembretes
* reduz no-show
* recupera faltosos
* centraliza atendimento humano
* respeita Meta e LGPD
* economiza custo operacional para a clínica
