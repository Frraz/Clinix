# Fluxo do Portal do Paciente — Clinix

> Jornada do paciente usando o portal: do primeiro login às interações recorrentes.

---

# 1. PERSONAS ENVOLVIDAS

* paciente
* recepcionista (cria pré-cadastro, se necessário)
* profissional (visualiza pré-anamnese, libera laudos)
* sistema (envio de links, notificações)

---

# 2. CANAIS DE ENTRADA

* link enviado via WhatsApp após cadastro
* convite por email (Resend)
* QR code impresso na recepção
* link em automação de pós-consulta

---

# 3. PRÉ-CONDIÇÕES

* paciente cadastrado no tenant
* consentimento LGPD do portal aceito
* email ou telefone válido

---

# 4. PRIMEIRO ACESSO

## 4.1 Login

Duas opções:

* **CPF + senha** (paciente recebe link para criar senha)
* **OTP via WhatsApp** (mais usado em região com baixa familiaridade digital)

OTP:

* paciente entra com CPF e telefone
* sistema envia código de 6 dígitos via WhatsApp (Evolution API)
* validade 5 minutos
* 3 tentativas

---

## 4.2 Onboarding rápido

Wizard de 4 passos:

1. confirma dados pessoais
2. aceita Termo LGPD da clínica
3. preferências de comunicação (canais: WhatsApp/email/push)
4. opt-in para marketing (opcional)

---

# 5. TELA INICIAL

## Seções

* **Minhas consultas** — próximas e histórico
* **Meus documentos** — receitas, atestados, laudos, planos alimentares
* **Meus pacotes** — em andamento (estética/odonto/fisio/nutri)
* **Pagamentos** — em aberto e pagos
* **Comunicação** — histórico de mensagens
* **Configurações** — dados pessoais, senha, preferências

---

# 6. AGENDAMENTO SELF-SERVICE

## Passos

1. paciente clica em "Agendar"
2. seleciona especialidade (somente as ativas na clínica)
3. seleciona profissional
4. visualiza horários disponíveis (consulta agenda em tempo real)
5. seleciona tipo de consulta
6. confirma
7. recebe confirmação por WhatsApp + tela de sucesso

## Regras

* horários respeitam bloqueios e férias
* lock otimista impede double-booking
* política de cancelamento exibida antes da confirmação
* sinaliza se paciente tem pacote ativo daquele tipo

---

# 7. CHECK-IN DIGITAL

24h antes da consulta:

* paciente recebe WhatsApp pedindo confirmação
* link abre check-in no portal
* paciente confirma presença
* opcional: preenche pré-anamnese (formulário do template da especialidade)

## Pré-anamnese

* renderização dinâmica baseada em `record-template.schema_json`
* resposta vira `RecordEntry` com `applied_via=portal`
* profissional vê ao abrir o prontuário antes da consulta

---

# 8. CONSULTA / SESSÃO

* paciente comparece
* recepção identifica via QR code do check-in (opcional)
* profissional atende
* prontuário registrado

---

# 9. PÓS-CONSULTA — RECEITAS E DOCUMENTOS

* receita gerada em PDF
* atestado se aplicável
* paciente recebe link via WhatsApp
* visualiza no portal sem novo login (token assinado)
* baixa PDF
* compartilha com farmácia / outro profissional

---

# 10. PLANO ALIMENTAR (NUTRIÇÃO)

* nutricionista publica plano
* paciente vê visualização interativa no portal
* baixa PDF
* preenche recordatório entre consultas (alimentação real)
* registra peso semanal
* recebe lembretes

---

# 11. PACOTES DE TRATAMENTO

Paciente vê:

* nome do pacote
* progresso (3/10 sessões)
* próxima sessão agendada
* fotos antes/depois (se autorizado)
* parcelas pagas e em aberto
* botão de agendar próxima sessão

---

# 12. PAGAMENTOS

* lista de cobranças em aberto e pagas
* botão "pagar agora" abre Mercado Pago
* PIX QR code ou cartão
* recibo gerado automaticamente

---

# 13. ESCALAS (PSICOLOGIA)

* paciente recebe link para responder escala antes da consulta
* renderiza formulário com itens validados
* score calculado no backend
* profissional vê ao abrir prontuário
* paciente vê score próprio (se a clínica permitir)

---

# 14. CONSENTIMENTOS

* aceitar consentimento LGPD básico
* aceitar termo para foto antes/depois (estética/dermato) — específico
* aceitar termo para uso em portfólio/marketing — opt-in separado
* histórico de termos aceitos disponível

---

# 15. NOTIFICAÇÕES

Configuráveis por paciente:

* WhatsApp
* email
* push (mobile)

Sempre respeitando opt-out.

---

# 16. SEGURANÇA

* HTTPS obrigatório
* sessão expira em 30 min de inatividade
* tokens de acesso a documentos com TTL curto
* nunca expor diagnóstico em URL
* registros de acesso (audit log)

---

# 17. ACESSIBILIDADE

* WCAG AA mínimo
* navegação por teclado
* contraste validado
* labels semânticos
* compatível com leitores de tela

Ver `docs/acessibilidade.md`.

---

# 18. PERFORMANCE

* primeira tela < 2s em 3G
* lista de consultas < 500ms
* lazy load de imagens
* PWA (instalável no celular)

---

# 19. KPIs

* % de pacientes que ativam o portal
* % que agendam self-service
* % que fazem check-in digital
* % de pré-anamnese preenchida
* tempo médio até atender (com pré-anamnese)
* NPS do portal

---

# 20. AUDITORIA

Eventos registrados:

* `patient_portal.login`
* `patient_portal.appointment_self_scheduled`
* `patient_portal.checkin_completed`
* `patient_portal.document_viewed`
* `patient_portal.payment_made`

---

# 21. O QUE EVITAR

* expor dados de outro paciente (RLS + RBAC)
* permitir acesso sem consentimento LGPD do portal
* mostrar diagnóstico sensível sem mediação
* manter sessão indefinidamente
* enviar PDF clínico em URL não-assinada

---

# 22. EXEMPLO REAL

Cenário: paciente da **Clínica Dra. Mariana Leite (dermato)** agenda rastreio anual.

1. Recebe WhatsApp com link
2. Login via OTP
3. Aceita Termo LGPD do portal
4. Agenda consulta para semana seguinte
5. Recebe lembrete D-1
6. Confirma via portal
7. Preenche pré-anamnese dermato (exposição solar, histórico)
8. Comparece, é atendida, mapa de lesões atualizado
9. Recebe receita tópica via portal
10. 6 meses depois recebe lembrete automático para seguimento

---

# 23. REFERÊNCIAS

* `docs/portal-paciente.md`
* `docs/auth.md`
* `docs/whatsapp.md`
* `docs/lgpd.md`
* `docs/imagens-clinicas.md`
* `docs/escalas-testes.md`
* `examples/fluxo-mfa.md`
