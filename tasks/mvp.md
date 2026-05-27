# MVP — Clinix

> Escopo, critérios de pronto e checklist do MVP do Clinix.

---

# 1. OBJETIVO DO MVP

Entregar um sistema **vendável** para a primeira clínica piloto em **qualquer das 8 especialidades-alvo**.

Não é protótipo.

Não é beta interno.

É produto que uma clínica real vai operar diariamente.

---

# 2. NICHO INICIAL

Foco no MVP:

* clínicas dentro das **8 especialidades-alvo** (ver seção 3)
* 1 unidade por clínica (multi-unidade fica para Fase 2)
* até 10 profissionais
* até 15 usuários
* operação majoritariamente particular (convênio simples opcional)
* clínicas multi-especialidade são suportadas habilitando mais de uma especialidade

Razão: a engine de prontuário composable serve as 8 áreas com a mesma base. Vender só para uma especialidade limita o tamanho do mercado regional sem reduzir significativamente o esforço.

---

# 3. ESPECIALIDADES-ALVO

O MVP deve atender:

1. **Clínicas médicas gerais** — SOAP padrão, prescrições, atestados
2. **Odontologia** — odontograma, planos de tratamento, orçamento, parcelamento
3. **Fisioterapia** — avaliação funcional, sessões, evolução, alta
4. **Psicologia** — escalas validadas (PHQ-9/GAD-7/DASS-21), evolução com sigilo
5. **Biomedicina / Laboratórios** — solicitação + entrega de laudo externo (operação interna completa é Fase 2.5)
6. **Dermatologia** — mapa corporal de lesões, dermatoscopia, fotos clínicas, prescrição tópica
7. **Clínicas de Estética** — pacotes de tratamento com sessões, fotos antes/depois, contraindicações
8. **Nutrição** — antropometria, bioimpedância, plano alimentar, envio ao paciente

Cada uma é servida pelo prontuário composable + componentes específicos. Detalhamento:

* `docs/prontuario.md`
* `docs/templates-clinicos.md`
* `docs/dermatologia.md`
* `docs/estetica.md`
* `docs/nutricao.md`
* `docs/odontograma.md`
* `docs/escalas-testes.md`
* `docs/pacotes-tratamento.md`
* `docs/antropometria.md`
* `docs/imagens-clinicas.md`

---

# 4. ESCOPO DO MVP

## ✅ Incluído

### Core
* multi-tenancy (row-based, middleware, manager)
* autenticação JWT + recuperação de senha
* MFA (TOTP) para admin e médico/profissional
* RBAC granular (incluindo permissões por especialidade)
* auditoria base + audit log reforçado em prontuário (psicologia, fotos clínicas)
* configurações da clínica (perfis, horários, especialidades ativas)

### Pacientes
* cadastro completo + timeline unificada
* anexos no R2
* consentimentos LGPD digitais
* anexo de fotos clínicas com consentimento por foto

### Agenda
* dia/semana/mês com FullCalendar
* multi-profissional
* encaixe e recorrência básica
* check-in / recepção
* status workflow completo
* bloqueios e férias

### Prontuário composable
* engine de templates por especialidade (`Specialty` + `RecordTemplate`)
* templates seed para as 8 especialidades
* anamnese estruturada e evolução SOAP/SOAP-like
* anotações privadas (psicologia) com flag `is_private`
* anexos
* timeline clínica
* versionamento básico

### Componentes específicos por especialidade
* odontograma (notação FDI)
* mapa corporal de lesões (dermatologia)
* aplicação de escalas validadas (psicologia)
* antropometria + bioimpedância (nutrição)
* plano alimentar (nutrição)
* pacotes de tratamento com sessões (estética, odonto, fisio)
* fotos antes/depois (estética, dermatologia)

### Documentos
* prescrições em PDF (sistêmicas, tópicas, dietéticas, fisioterapêuticas)
* atestados em PDF
* recibos em PDF
* todos com branding do tenant

### Financeiro essencial
* cobrança por consulta ou pacote
* parcelamento de pacote de tratamento
* PIX e cartão (Mercado Pago)
* recibo automático
* dashboard de caixa do dia

### Laboratório (escopo MVP)
* solicitação de exame
* upload de laudo externo
* entrega via portal + WhatsApp
* (operação laboratorial completa — coleta, triagem, processamento, validação técnica — vai para Fase 2.5)

### WhatsApp transacional
* Evolution API integrada
* confirmação automática, lembrete D-1 e 2h antes
* pós-consulta
* envio de receita / atestado / plano alimentar
* recuperação de faltoso
* opt-out reconhecido

### Portal do paciente
* login (CPF + senha ou OTP via WhatsApp)
* agendamento self-service
* check-in digital
* download de receitas, atestados, laudos
* visualização do plano alimentar (nutrição)
* visualização de pacotes em andamento
* pagamento via link

### Dashboards operacionais
* agenda do dia
* caixa do dia
* consultas concluídas no mês
* produção por profissional

### Personalização básica
* logo + cor primária + dados da clínica
* templates de PDF aplicam branding

### Import / Export
* import de pacientes via CSV
* export operacional (pacientes, agenda, financeiro) em CSV/PDF
* export completo do tenant (LGPD)

---

## ❌ Fora do MVP

* TISS e convênios complexos (Fase 2)
* CRM com pipeline e funis (Fase 2)
* automações low-code (editor visual) — Fase 2
* IA clínica — Fase 3
* mobile — Fase 4
* telemedicina — Fase 4
* multi-unidade (Fase 2)
* operação laboratorial interna completa (coleta, triagem, processamento) — Fase 2.5
* assinatura digital ICP-Brasil (Fase 2) — MVP usa assinatura simples
* BI avançado (Fase 3)
* marketplace de integrações (Fase 5)
* SSO enterprise (Fase 6)

---

# 5. CRITÉRIOS DE PRONTO POR MÓDULO

## Core

* [ ] middleware multi-tenant funcionando
* [ ] manager filtra por tenant automaticamente
* [ ] middleware bloqueia request sem tenant
* [ ] testes de isolamento passando no CI
* [ ] audit log gravando ações críticas
* [ ] permissões por especialidade aplicadas

---

## Auth

* [ ] login com email/senha
* [ ] refresh token funcionando
* [ ] MFA TOTP para admin/profissional
* [ ] reset de senha por email
* [ ] logout invalida refresh
* [ ] rate limiting em endpoints sensíveis

---

## RBAC

* [ ] roles iniciais cadastradas (admin, gestor, recepcionista, profissional clínico por especialidade, financeiro)
* [ ] permissões por módulo/ação
* [ ] frontend esconde ações sem permissão
* [ ] backend valida em todo endpoint
* [ ] acesso a anotações privadas (psicologia) restrito ao profissional

---

## Pacientes

* [ ] cadastro completo (dados, endereço, contatos, convênio, responsáveis)
* [ ] busca rápida com pg_trgm
* [ ] timeline carrega < 500ms para histórico médio
* [ ] anexos com upload no R2
* [ ] consentimentos LGPD registrados
* [ ] consentimento específico por foto clínica

---

## Agenda

* [ ] visualizações dia/semana/mês
* [ ] drag-and-drop funcional
* [ ] sem conflitos (lock otimista)
* [ ] check-in / chamada
* [ ] status workflow completo
* [ ] bloqueios e férias
* [ ] integração com pacote de tratamento (consumir 1 sessão)

---

## Prontuário composable

* [ ] engine de templates funcional
* [ ] template seed para cada uma das 8 especialidades
* [ ] dynamic form valida `content_json` contra `schema_json`
* [ ] anotações com flag `is_private` (psicologia)
* [ ] anexos
* [ ] timeline clínica
* [ ] versionamento básico

---

## Componentes específicos

* [ ] odontograma com FDI
* [ ] mapa corporal de lesões (dermato)
* [ ] escalas validadas (PHQ-9, GAD-7, DASS-21)
* [ ] antropometria com gráfico de evolução
* [ ] plano alimentar com versionamento
* [ ] pacote de tratamento com N sessões
* [ ] foto antes/depois com slider de comparação

---

## Documentos

* [ ] prescrição em PDF (4 tipos: sistêmica, tópica, dietética, fisio)
* [ ] atestado em PDF
* [ ] recibo em PDF
* [ ] todos com branding do tenant
* [ ] assinatura simples visível (nome + registro)

---

## Financeiro

* [ ] cobrança automática na conclusão da consulta
* [ ] cobrança manual
* [ ] cobrança parcelada por pacote
* [ ] pagamento PIX (Mercado Pago)
* [ ] pagamento cartão (Mercado Pago)
* [ ] webhook de confirmação idempotente
* [ ] recibo em PDF
* [ ] dashboard de caixa do dia

---

## WhatsApp

* [ ] integração Evolution API funcionando
* [ ] templates de confirmação aprovados
* [ ] envio assíncrono via Celery
* [ ] webhook de status
* [ ] histórico no painel
* [ ] opt-out reconhecido
* [ ] envio de PDFs anexados (receita, atestado, plano alimentar)

---

## Dashboards

* [ ] agenda do dia
* [ ] pacientes aguardando
* [ ] caixa do dia
* [ ] consultas concluídas no mês
* [ ] produção por profissional

---

## Portal do Paciente

* [ ] login do paciente
* [ ] agendamento self-service
* [ ] visualização de próximas consultas
* [ ] download de receitas, atestados, laudos
* [ ] visualização do plano alimentar
* [ ] visualização de pacotes em andamento
* [ ] pagamento via link

---

## Personalização

* [ ] upload de logo
* [ ] escolha de cor primária
* [ ] dados completos da clínica (CNPJ, endereço, contatos, RT)
* [ ] PDFs aplicam branding
* [ ] templates de WhatsApp/email personalizáveis

---

## Import / Export

* [ ] import de pacientes via CSV com mapeamento
* [ ] export operacional CSV/PDF por módulo
* [ ] export completo do tenant (LGPD)

---

# 6. CRITÉRIOS DE QUALIDADE

## Performance

* p95 < 500ms em endpoints críticos
* primeira tela carrega < 2s
* upload de foto < 3s (com presigned + Celery para thumbs)

---

## Segurança

* HTTPS obrigatório
* MFA habilitável
* audit log gravando (incluindo leitura de dados privados)
* secrets em .env (nunca commitados)
* testes de isolamento passando
* consentimento LGPD digital por foto/dado sensível

---

## Testes

* cobertura mínima 70% em services e regras de negócio
* testes E2E para fluxos: login, agendar, atender em cada uma das 8 especialidades, cobrar, enviar WhatsApp
* testes de tenancy obrigatórios

---

## Observabilidade

* Sentry capturando erros (backend + frontend)
* logs estruturados em JSON
* `request_id` propagado
* healthcheck `/api/healthz` e `/api/readyz`

---

## Deploy

* docker-compose subindo todos os serviços
* CI/CD com testes obrigatórios
* deploy automatizado em staging
* deploy manual em produção (botão verde)
* rollback em < 5min

---

# 7. CRITÉRIO DE CONCLUSÃO DO MVP

O MVP estará pronto quando:

* uma clínica real (de qualquer das 8 especialidades) está usando em produção
* completaram 30 dias sem incidente crítico
* feedback estruturado coletado
* NPS interno > 8
* roadmap da Fase 1.5 ajustado com base no feedback

---

# 8. ANTI-METAS

NÃO vamos no MVP:

* construir editor visual de workflow
* construir mobile
* construir laboratório com operação interna completa (coleta/processamento)
* construir IA
* customizar por clínica fora do permitido em personalização
* lançar para 10 clínicas de uma vez
* implementar TISS

---

# 9. PROCESSO DE VALIDAÇÃO

* clínica piloto identificada por especialidade (idealmente 1 piloto de cada das 8 áreas)
* contrato simbólico ou gratuito nos primeiros 90 dias
* onboarding presencial ou remoto
* contato semanal de feedback
* iteração rápida sobre os bloqueios reais

---

# 10. CRONOGRAMA INDICATIVO

* **Sprint 01** (2–3 sem) — fundação técnica (ver `tasks/sprint-01.md`)
* **Sprint 02** (3–4 sem) — auth completo + pacientes + agenda base (ver `tasks/sprint-02.md`)
* **Sprint 03** (3–4 sem) — prontuário composable + financeiro essencial (ver `tasks/sprint-03.md`)
* **Sprint 04** (3–4 sem) — WhatsApp + portal + componentes específicos por especialidade (ver `tasks/sprint-04.md`)

Total estimado: **3–4 meses** até MVP em produção.

---

# 11. PRÓXIMA FASE

Após MVP concluído, ver `docs/roadmap.md` para Fase 1.5 (polimento) e Fase 2 (expansão).
