# Fluxo de Onboarding — Clinix

> Jornada de uma clínica nova entrando no Clinix: do cadastro à primeira consulta em até 7 dias.

---

# 1. PERSONAS ENVOLVIDAS

* admin da clínica (geralmente responsável técnico ou gestor)
* CS / suporte Clinix (acompanha)
* recepcionista (treinada ao final)
* profissionais clínicos (cadastrados durante o processo)
* sistema (wizard + automações)

---

# 2. CANAIS DE ENTRADA

* compra direta no site clinix.app
* indicação de cliente atual
* lead trabalhado pelo time comercial
* migração de outro sistema (iClinic, Doctoralia, Ninsaúde, planilha)

---

# 3. PRÉ-CONDIÇÕES

* contrato e plano aceito
* método de pagamento configurado (Stripe)
* dados básicos da clínica disponíveis (CNPJ, endereço, RT)

---

# 4. ETAPAS DO WIZARD

## 4.1 Dia 0 — Criação da conta

* admin recebe email com link de criação de senha
* aceita Termos de Uso e Política de Privacidade
* faz login pela primeira vez
* MFA recomendado (TOTP) — pode adiar até D+3

---

## 4.2 Dia 0–1 — Wizard inicial (10 passos)

1. **Dados da clínica** — razão social, CNPJ, nome fantasia, endereço, contatos, responsável técnico (CRM/CRO/CRBM/CRP/CRN/CREFITO)
2. **Especialidades ativas** — seleciona entre as 8 (pode habilitar várias)
3. **Branding** — logo, cor primária, slogan opcional
4. **Profissionais** — adiciona profissionais com role + especialidade
5. **Horários de funcionamento** — agenda padrão por dia da semana
6. **Templates de prontuário** — confirma quais templates seed serão usados; pode estender
7. **WhatsApp** — conecta número via Evolution API (QR code para autenticar)
8. **Pagamentos** — conecta Mercado Pago (chave de produção)
9. **Política de cancelamento** — define regras (no-show, reagendamento, prazos)
10. **Termos LGPD** — texto do consentimento padrão e termos específicos por procedimento (se aplicável)

Ao final: tela de celebração + checklist pós-wizard.

---

## 4.3 Dia 1–3 — Checklist pós-wizard

* [ ] cadastrar pelo menos 10 pacientes (import ou manual)
* [ ] cadastrar pacotes de tratamento (se estética/odonto/fisio)
* [ ] revisar templates clínicos
* [ ] aprovar templates de WhatsApp na Meta
* [ ] testar fluxo: agendar → atender → cobrar → enviar receita
* [ ] habilitar MFA para admin (se ainda não habilitado)

---

## 4.4 Dia 2–5 — Import de dados (se aplicável)

* upload de CSV com pacientes
* mapeamento de colunas
* validação (CPF, datas, telefones)
* preview + correções
* import em background (Celery)
* relatório de sucesso/falhas
* anexos de prontuário em ZIP (opcional)

Ver `docs/importacao-exportacao.md`.

---

## 4.5 Dia 3–5 — Treinamento

* sessão de onboarding com CS (Zoom/Meet)
* vídeos curtos no painel (in-app)
* base de conhecimento
* checklist por papel (admin, recepcionista, profissional)

---

## 4.6 Dia 5–7 — Go-live

* primeira consulta real agendada
* atendimento executado fim a fim
* feedback estruturado coletado
* ajustes finos com CS

---

# 5. AUTOMAÇÕES OPERACIONAIS

* email de boas-vindas no Dia 0
* email/WhatsApp lembrando próximo passo do wizard (D+1, D+3)
* mensagem de check-in do CS (D+7)
* NPS na D+30

---

# 6. INTEGRAÇÕES

* Stripe — billing
* Mercado Pago — cobranças operacionais
* Evolution API — WhatsApp
* R2 — storage de logo e import inicial
* Resend — emails transacionais

---

# 7. REGRAS DE NEGÓCIO

* trial 14 dias sem cartão obrigatório
* downgrade automático para Starter no final do trial sem upgrade
* dados nunca apagados em downgrade — funcionalidades são desativadas
* tenant inativo > 90 dias notificado, > 180 dias arquivado com aviso

---

# 8. OBSERVABILIDADE

Eventos relevantes:

* `tenant.created`
* `onboarding.step_completed`
* `onboarding.wizard_completed`
* `tenant.first_appointment`
* `tenant.go_live`
* `tenant.churned` (na ida)

Audit log registra tudo. Métricas exibidas em dashboard de CS.

---

# 9. KPIs DE ONBOARDING

* tempo médio até wizard completo
* tempo médio até primeira consulta real
* % de clínicas que ativam WhatsApp
* % de clínicas que importam pacientes
* taxa de conversão trial → paid
* NPS no D+30

---

# 10. SE ALGO DER ERRADO

* wizard incompleto > 48h → ping automático
* falha em conectar WhatsApp → escalada para CS
* import com > 10% de falhas → bloqueio + revisão manual
* primeira consulta não acontece em D+7 → alerta de risco

---

# 11. REGRAS DE LGPD

* todos os dados importados ficam no tenant da clínica
* clínica é controlador, Clinix é operador
* consentimento dos pacientes deve ter sido obtido (a clínica declara)
* consentimentos adicionais coletados via portal/WhatsApp em primeiro contato

---

# 12. EXEMPLO REAL (RONDON DO PARÁ)

Cenário: **Clinic Day** (estética) entra no Clinix.

* Dia 0 — admin cria conta, ativa especialidade `aesthetics`
* Dia 1 — completa wizard, cadastra 3 esteticistas e 1 recepcionista
* Dia 1 — importa CSV com 200 pacientes
* Dia 2 — cadastra pacotes: "Drenagem 10 sessões", "Limpeza de pele 4 sessões"
* Dia 3 — treina equipe via Zoom
* Dia 4 — primeira sessão real (drenagem com fotos antes/depois)
* Dia 7 — operação 100% no Clinix

---

# 13. REFERÊNCIAS

* `docs/onboarding.md`
* `docs/importacao-exportacao.md`
* `docs/personalizacao.md`
* `docs/billing.md`
* `examples/fluxo-mfa.md`
