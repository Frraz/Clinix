# Notificações — Clinix

> Estratégia geral de notificações multi-canal do Clinix.

---

# 1. VISÃO GERAL

Notificações são camada transversal do sistema.

Atendem:

* operação interna (alertas, lembretes, atribuições)
* comunicação com paciente (confirmação, lembrete, cobrança)
* eventos críticos (incidentes, problemas operacionais)
* engajamento (newsletter, novidades)

Este doc cobre notificações **não-WhatsApp**.

Para WhatsApp ver `docs/whatsapp.md`.

---

# 2. OBJETIVOS

Garantir:

* notificação certa para a pessoa certa
* canal apropriado por urgência
* preferências respeitadas
* não-invasivo
* rastreável
* baixo custo operacional

---

# 3. CANAIS

| Canal | Uso | Quando |
|-------|-----|--------|
| In-app | painel de notificações interno | sempre disponível para usuário logado |
| Email | comunicação detalhada | resultados, relatórios, comprovantes |
| WhatsApp | transacional pessoal | confirmações, lembretes, cobranças |
| SMS | fallback crítico | quando WhatsApp falha em mensagens críticas |
| Push (mobile) | imediato no celular | quando app instalado (Fase 4) |
| Webhook | integrações | sistemas externos |

---

# 4. PRINCÍPIOS

Toda notificação deve:

* ter destinatário claro
* ter canal apropriado
* ter conteúdo claro
* respeitar preferências
* ter rastreabilidade
* ser assíncrona
* nunca conter dado sensível em payload de push

---

# 5. CATEGORIAS

## Transacionais

Disparadas por evento operacional.

Sempre enviadas (mesmo com opt-out de marketing).

Exemplos:

* consulta confirmada
* resultado de exame disponível
* pagamento confirmado
* nova mensagem

---

## Operacionais

Internas à equipe da clínica.

Exemplos:

* fila acumulada
* tarefa atribuída
* incidente
* limite de plano atingido

---

## Marketing

Promocionais, exigem opt-in.

Exemplos:

* newsletter
* novidades do produto
* convite a webinar
* aniversário (paciente, com opt-in)

---

## Sistema

Notificações do Clinix para os usuários.

Exemplos:

* manutenção programada
* incidente em curso
* nova versão
* mudança de termos

---

# 6. IN-APP

## Painel de notificações

* ícone no header com badge
* drawer com lista cronológica
* filtros (não lida, atribuída a mim, sistema, paciente)
* marcar como lida
* marcar todas como lidas
* arquivar

---

## Persistência

* notificações ficam por 90 dias
* arquivadas vão para fundo de tela
* exportáveis em CSV (auditoria)

---

# 7. EMAIL

## Stack

* Resend como provedor
* React Email para templates
* SPF, DKIM, DMARC obrigatórios
* domínio próprio (`no-reply@clinix.com.br`)
* opção de domínio do tenant (Fase 2 — Business+)

---

## Templates

* boas-vindas
* confirmação de consulta
* comprovante de pagamento
* receita médica anexada
* laudo disponível
* aniversário
* reset de senha
* alteração de senha
* convite de usuário
* aviso LGPD

Branding do tenant aplicado.

---

## Métricas

* taxa de entrega
* taxa de abertura
* taxa de clique
* reclamações de spam
* bounces (hard/soft)

---

# 8. SMS

## Stack

* Twilio ou Zenvia como provedor
* uso restrito por custo

---

## Quando usar

* OTP de MFA
* recuperação de senha quando email indisponível
* fallback crítico se WhatsApp falhar (cobrança, laudo crítico)

---

## Quando NÃO usar

* lembretes diários (WhatsApp é mais barato)
* marketing
* mensagens longas

---

# 9. PUSH (FASE 4)

## Stack

* Expo Notifications + FCM (Android) + APNs (iOS)

---

## Princípios

* payload curto e neutro ("Nova mensagem")
* nunca dado sensível no payload
* deep link autenticado
* opt-in granular por categoria

Ver `docs/mobile.md`.

---

# 10. WEBHOOK PARA O CLIENTE

A clínica pode configurar:

* webhooks para eventos do sistema
* assinatura HMAC
* retry com backoff
* dead-letter queue
* visualização de tentativas

Ver `docs/api.md`.

---

# 11. PREFERÊNCIAS DO USUÁRIO

Cada usuário interno e cada paciente configura:

| Categoria | In-app | Email | WhatsApp | SMS | Push |
|-----------|--------|-------|----------|-----|------|
| Transacional | ✅ obrigatório | opcional | opcional | opcional | opcional |
| Operacional | ✅ obrigatório | opcional | — | — | opcional |
| Marketing | opcional | opcional | opcional | — | opcional |
| Sistema | ✅ obrigatório | obrigatório | — | — | — |

---

# 12. OPT-IN E OPT-OUT

## Opt-in

* explícito para marketing
* registrado em audit
* versão da política aceita
* timestamp

---

## Opt-out

* fácil em todos os canais
* link de descadastro em emails
* palavra-chave em WhatsApp/SMS
* botão no portal do paciente
* respeito imediato

---

# 13. JANELA DE ENVIO

Notificações ao paciente respeitam horário:

* 8h às 21h (default)
* configurável pela clínica
* nunca de madrugada
* exceção para emergências (resultado crítico)

---

# 14. AGREGAÇÃO

Evitar bombardear o usuário:

* múltiplas notificações em pouco tempo → digest
* email digest diário/semanal opcional
* push agregado ("3 novas mensagens")

---

# 15. PRIORIZAÇÃO

| Prioridade | Característica |
|-----------|---------------|
| Crítica | bypass de janela, multi-canal, sem agregação |
| Alta | canal preferido + fallback |
| Normal | canal preferido |
| Baixa | apenas in-app |

Exemplos:

* Crítica → resultado crítico de exame, incidente de segurança
* Alta → confirmação de consulta, lembrete 2h antes
* Normal → cobrança preventiva
* Baixa → newsletter

---

# 16. RASTREAMENTO

Cada notificação registra:

* destinatário
* canal
* template
* parâmetros
* status (queued, sent, delivered, read, failed, opted_out)
* timestamps
* tenant
* origem (evento, manual, automação)

Tabela `notifications` com particionamento mensal.

---

# 17. FILAS

Stack:

* Celery + Redis
* fila dedicada por canal (email, sms, push, in_app)
* retry com backoff exponencial
* dead-letter queue
* alerta a admin se fila acumular

---

# 18. PERFORMANCE

Metas:

* in-app: aparece em < 5s
* email: enviado em < 30s
* WhatsApp: enviado em < 10s
* push: < 5s
* nenhum endpoint da API espera notificação

---

# 19. TEMPLATES

Versionados em código:

* nunca editáveis em produção sem PR
* testáveis em ambiente de staging
* preview disponível para clínica antes de uso
* A/B testing possível em campanhas (Fase 3)

---

# 20. INTEGRAÇÃO COM AUTOMAÇÕES

Notificações são uma das ações primárias do motor de workflows.

Workflows publicam → motor envia.

Ver `docs/workflows.md`.

---

# 21. INTEGRAÇÃO COM CRM

Eventos de notificação alimentam CRM:

* paciente leu mensagem
* paciente ignorou cobrança 3x
* paciente clicou em link de retorno

Ver `docs/crm.md`.

---

# 22. SEGURANÇA

* payload nunca contém dado sensível bruto
* preferir links autenticados com TTL
* TLS obrigatório em todos os canais
* secrets de providers em env
* nunca expor token entre tenants

---

# 23. LGPD

* opt-out respeitado sempre
* link de descadastro obrigatório em marketing
* base legal registrada por categoria
* histórico de consentimento por canal

Ver `docs/lgpd.md`.

---

# 24. CUSTOS

Controle por tenant:

* limite mensal por canal
* alerta ao atingir 80%
* upgrade ou cobrança de excedente
* dashboard de uso

---

# 25. ROADMAP

## Fase 1 (MVP)

* in-app básico
* email transacional
* WhatsApp transacional (ver `docs/whatsapp.md`)
* preferências básicas no perfil

---

## Fase 1.5

* SMS para OTP
* digest de email semanal
* templates com branding completo

---

## Fase 2

* webhooks para o cliente
* SMS de fallback crítico
* campanhas de marketing por email
* digest configurável

---

## Fase 3

* A/B testing de templates
* IA sugere melhor horário/canal
* personalização de tom

---

## Fase 4

* push notifications mobile

---

# 26. O QUE EVITAR

Nunca permitir:

* notificação sem opt-in para marketing
* canal inadequado (SMS de marketing, email de OTP)
* payload sensível em push
* notificação cruzando tenants
* envio fora da janela sem ser crítico
* spam (mais de N por categoria por dia)

---

# 27. OBJETIVO FINAL

Construir uma camada de notificações que:

* a pessoa certa recebe a coisa certa no canal certo
* respeita rigorosamente preferências e LGPD
* nunca incomoda
* é rastreável
* tem custo previsível
* serve como motor para automações e engajamento
