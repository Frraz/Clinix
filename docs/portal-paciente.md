# Portal do Paciente — Clinix

> Arquitetura e diretrizes do portal do paciente do Clinix.

---

# 1. VISÃO GERAL

O Portal do Paciente é a interface do Clinix voltada ao paciente final.

NÃO é o painel da clínica.

É um produto à parte com:

* objetivos próprios
* UX própria
* segurança própria
* identidade visual da clínica (branding)
* mínimo viável funcional desde o MVP

---

# 2. OBJETIVOS

Garantir:

* paciente acessa próprios dados com segurança
* paciente agenda, paga e baixa documentos sem ligar pra clínica
* paciente exerce direitos LGPD
* clínica reduz carga operacional
* paciente percebe modernidade da clínica

---

# 3. PÚBLICO

Pacientes da clínica:

* qualquer idade (UX precisa funcionar para 60+)
* sem treinamento prévio
* podem acessar via mobile ou desktop
* podem ter conexão lenta
* podem ter pouca familiaridade com tecnologia

---

# 4. PRINCÍPIOS

O portal deve:

* ser extremamente simples
* funcionar em conexão ruim
* respeitar dados sensíveis
* refletir a identidade da clínica
* nunca expor jargão clínico ou técnico
* nunca expor estrutura interna do Clinix

---

# 5. ARQUITETURA

* Next.js (mesma stack do painel operacional)
* rotas dedicadas em `app/(portal)/`
* layout próprio, sem sidebar do painel
* tema aplicado a partir do tenant
* autenticação separada (perfil `patient`)
* tokens JWT com claims diferentes

---

# 6. URLS

## Padrão

```
{slug}.clinix.com.br/portal
```

---

## Domínio próprio (Fase 2)

```
portal.clinicaexemplo.com.br
```

Via CNAME + Cloudflare SSL.

Ver `docs/personalizacao.md`.

---

# 7. AUTENTICAÇÃO

## Login

* email + senha
* OU CPF + senha
* OU código OTP via WhatsApp/SMS (passwordless — Fase 2)

---

## Primeiro acesso

* convite enviado pela clínica
* link único com TTL
* paciente define senha
* opção de habilitar MFA

---

## Recuperação

* reset por email
* reset por SMS (Fase 2)
* nunca expor se o email existe ("Se cadastrado, enviaremos...")

---

# 8. FUNCIONALIDADES MVP

## Agendamento

* ver próximas consultas
* agendar nova consulta
* selecionar profissional, especialidade, horário
* confirmar/cancelar/reagendar
* histórico de consultas

---

## Documentos

* baixar receitas em PDF
* baixar atestados em PDF
* baixar laudos
* baixar comprovantes financeiros
* histórico completo de documentos

---

## Financeiro

* ver cobranças em aberto
* pagar via PIX, cartão, boleto
* histórico de pagamentos
* baixar recibos

---

## Perfil

* visualizar próprios dados
* editar contatos e endereço
* alterar senha
* gerenciar MFA

---

# 9. FUNCIONALIDADES FASE 2

* pré-anamnese antes da consulta
* check-in digital (QR code)
* upload de exames externos
* compartilhamento de prontuário com outro profissional
* mensagens com a clínica
* lembretes opt-in

---

# 10. FUNCIONALIDADES FASE 3

* telemedicina (quando o módulo entrar)
* chat com IA (FAQ da clínica)
* histórico clínico organizado pelo paciente
* compartilhamento com app de saúde (Apple Health, Google Fit)

---

# 11. LGPD NO PORTAL

Cumpre obrigações do art. 18:

* visualizar próprios dados
* solicitar correção
* solicitar exclusão (com fluxo de retenção legal)
* solicitar portabilidade (export completo)
* visualizar consentimentos ativos
* revogar consentimento (marketing)
* visualizar histórico de acessos

Ver `docs/lgpd.md`.

---

# 12. SEGURANÇA

* HTTPS obrigatório
* JWT de paciente NÃO acessa endpoints do painel
* permissões isoladas (só vê dados do próprio paciente)
* rate limiting agressivo (anti-scraping)
* CAPTCHA em ações sensíveis
* MFA opcional (recomendado)
* logs de acesso

---

# 13. UX

## Princípios

* tipografia maior (acessibilidade para idosos)
* 1 ação principal por tela
* linguagem direta, sem jargão
* feedback imediato
* botões com labels claros ("Pagar", "Baixar receita", "Confirmar consulta")
* nunca usar termos como "tenant", "ID", "endpoint"

---

## Mobile-first

* portal é majoritariamente acessado por celular
* layout otimizado para tela pequena
* tap targets generosos (≥ 44pt)
* sem hover dependente
* funciona bem em 3G

---

# 14. BRANDING

Aplica personalização da clínica:

* logo no header
* cores
* mensagem de boas-vindas
* endereço e contato no rodapé
* link para contato (WhatsApp da clínica)

Ver `docs/personalizacao.md`.

---

# 15. NOTIFICAÇÕES

Paciente pode receber:

* WhatsApp (transacional)
* email
* SMS (Fase 2)
* push no app mobile (Fase 4)

Preferências configuráveis no perfil.

---

# 16. PERMISSÕES NO PORTAL

Paciente vê APENAS:

* próprios dados
* próprias consultas
* próprios documentos
* próprias cobranças

Família/responsável (futuro):

* responsável legal de menor pode ver dependentes
* configurável pela clínica

---

# 17. PAGAMENTOS

Integração com Mercado Pago:

* PIX com QR code
* cartão (tokenizado, nunca armazenado)
* boleto
* parcelamento quando ativo
* recibo automático após pagamento

---

# 18. UPLOAD DE EXAMES EXTERNOS

Fase 2:

* paciente envia PDF/imagem
* aguarda revisão da clínica
* anexado ao prontuário após aprovação
* nunca aparece no prontuário sem revisão clínica

---

# 19. PRÉ-ANAMNESE

Fase 2:

* clínica configura questionário
* paciente recebe link antes da consulta
* responde no portal
* respostas chegam organizadas no prontuário
* IA pode resumir (Fase 3)

---

# 20. ANIVERSÁRIO E LEMBRETES

Toques humanos:

* mensagem de aniversário (opt-in)
* lembrete de exames anuais (mamografia, papanicolau, check-up)
* lembrete de retorno

Tudo respeita opt-in.

---

# 21. AUDITORIA

Toda ação registrada:

* login
* visualização de documento
* download de PDF
* alteração de dados
* solicitação LGPD
* tentativas de login falhas

Visível para clínica + DPO.

---

# 22. PERFORMANCE

Metas:

* primeira tela < 1.5s em 3G
* navegação fluida em mobile antigo
* PDFs carregam via URL assinada CDN

---

# 23. ACESSIBILIDADE

WCAG AA obrigatório:

* contraste validado
* navegação por teclado
* labels semânticos
* fontes legíveis
* sem dependência de cor
* compatibilidade com leitores de tela

Ver `docs/acessibilidade.md`.

---

# 24. INTERNACIONALIZAÇÃO

PT-BR no MVP.

Arquitetura preparada para:

* espanhol (mercado regional)
* inglês

---

# 25. RECUPERAÇÃO DE CONTA

Quando paciente esquece tudo:

* recovery via email/SMS
* verificação adicional (CPF + data de nascimento)
* contato com a clínica como último recurso
* nunca expor dados sem confirmação

---

# 26. WEBHOOK PARA A CLÍNICA

Eventos do portal:

* `patient.self_registered`
* `patient.profile_updated`
* `patient.appointment_self_scheduled`
* `patient.payment_completed`
* `patient.document_downloaded`
* `patient.lgpd_request_filed`

Alimentam workflows e CRM.

---

# 27. INTEGRAÇÃO COM APP MOBILE

Quando o app mobile entrar (Fase 4):

* mesmas funcionalidades do portal
* push notifications
* biometria
* offline básico

Ver `docs/mobile.md`.

---

# 28. O QUE EVITAR

Nunca permitir:

* exposição de dados de outro paciente
* mostrar prontuário com termos técnicos sem contexto
* paciente alterar dados clínicos
* paciente ver dados internos da clínica
* links de documento sem TTL
* login compartilhável entre pacientes

---

# 29. ROADMAP

## Fase 1 (MVP)

* login + recuperação
* perfil
* agenda (ver, criar, cancelar)
* documentos (baixar)
* pagamentos básicos
* solicitações LGPD básicas

---

## Fase 1.5

* branding completo aplicado
* mobile-first refinado
* upload de exames externos

---

## Fase 2

* pré-anamnese
* check-in digital
* chat com clínica
* OTP passwordless
* domínio próprio
* dependentes (responsável legal)

---

## Fase 3

* telemedicina
* IA assistiva (FAQ, lembretes inteligentes)
* histórico organizável pelo paciente

---

## Fase 4

* app mobile (iOS + Android)

---

# 30. OBJETIVO FINAL

Construir um portal do paciente que:

* o paciente usa sem ligar pra clínica
* a clínica usa como diferencial de venda
* respeita rigorosamente LGPD
* parece um produto da clínica (não do Clinix)
* funciona em qualquer dispositivo
* economiza tempo da recepção
