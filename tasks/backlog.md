# Backlog — Clinix

> Backlog priorizado de funcionalidades, melhorias e ideias do Clinix.

---

# 1. ORGANIZAÇÃO

Backlog é organizado por fase do roadmap.

Ver `docs/roadmap.md` (master index) e `docs/roadmap/` (detalhe por fase) para contexto.

Sprints planejadas:

* sprints 01–08 já existem como arquivos em `tasks/`
* sprints 09+ ficam no `docs/roadmap/04-expansao.md` e seguintes até entrarem no horizonte de 2 sprints

Cada item:

* tem prioridade (P0, P1, P2, P3)
* tem fase alvo
* pode virar sprint quando madurada

---

# 2. PRIORIDADES

* **P0** — bloqueia próxima fase
* **P1** — alto valor, próximo a entrar
* **P2** — valor médio, planejado
* **P3** — ideia, ainda não detalhada

---

# 3. FASE 0 — FUNDAÇÃO

## P0

* [ ] setup Docker Compose com todos os serviços
* [ ] Django base + DRF + Postgres + Redis + Celery
* [ ] middleware multi-tenant + TenantAwareManager
* [ ] modelo base TenantAwareModel
* [ ] autenticação JWT com SimpleJWT
* [ ] RBAC base com roles iniciais
* [ ] audit log estrutural (django-auditlog)
* [ ] design system inicial Next.js + shadcn/ui
* [ ] CI/CD básico com GitHub Actions
* [ ] Sentry configurado em backend e frontend
* [ ] healthcheck endpoints
* [ ] backup automatizado diário do Postgres para R2
* [ ] runbook de DR documentado
* [ ] axe-core no CI (acessibilidade)
* [ ] performance budgets configurados no CI

---

## P1

* [ ] MFA TOTP via django-otp
* [ ] rate limiting via django-ratelimit
* [ ] criptografia AES em campos sensíveis
* [ ] testes de isolamento marcados `pytest -m tenancy`
* [ ] template de email transacional via Resend
* [ ] backup semanal em provedor independente (B2)
* [ ] status page básica
* [ ] documentação pública dos schemas de import/export

---

# 4. FASE 1 — MVP

> O MVP deve atender as 8 especialidades-alvo. Ver `tasks/mvp.md` para escopo completo.

## P0 — Núcleo

* [ ] cadastro de pacientes (CRUD completo)
* [ ] busca de pacientes com pg_trgm
* [ ] timeline do paciente
* [ ] agenda com FullCalendar (dia/semana/mês)
* [ ] drag-and-drop na agenda
* [ ] criação de agendamento com validação de conflito
* [ ] check-in / chamada
* [ ] cobrança automática pós-consulta
* [ ] integração Mercado Pago (PIX, cartão)
* [ ] integração Evolution API
* [ ] templates de WhatsApp aprovados
* [ ] envio assíncrono via Celery
* [ ] webhook de status WhatsApp
* [ ] dashboard operacional do dia
* [ ] portal do paciente base (login, agendar, ver receitas, ver pacotes, ver plano alimentar)
* [ ] upload de logo + cor primária da clínica
* [ ] cadastro completo de dados da clínica (CNPJ, endereço, contatos, responsável técnico)
* [ ] PDFs gerados aplicam branding do tenant (logo, cores, contato, responsável técnico)
* [ ] import básico de pacientes via CSV (mapeamento de colunas + validação)
* [ ] export operacional CSV/PDF por módulo (pacientes, agenda, financeiro)
* [ ] export completo do tenant (solicitação manual, geração assíncrona)

---

## P0 — Prontuário composable

* [ ] engine `Specialty` + `RecordTemplate` + `RecordEntry`
* [ ] schema dinâmico validado contra `schema_json`
* [ ] anexos no R2 com presigned PUT
* [ ] prescrição em PDF (WeasyPrint) — 4 tipos: sistêmica, tópica, dietética, fisioterapêutica
* [ ] atestado em PDF
* [ ] templates seed para as 8 especialidades-alvo
* [ ] versionamento por entrada
* [ ] anotações com flag `is_private` (psicologia) + audit log reforçado

---

## P0 — Componentes específicos por especialidade

* [ ] odontograma (FDI) — odontologia
* [ ] mapa corporal de lesões (SVG) — dermatologia
* [ ] aplicação de escalas validadas (PHQ-9, GAD-7, DASS-21) — psicologia
* [ ] antropometria + bioimpedância — nutrição
* [ ] editor de plano alimentar — nutrição
* [ ] pacote de tratamento com N sessões + parcelamento — estética, odonto, fisio
* [ ] foto antes/depois com slider — estética, dermatologia
* [ ] checklist de contraindicações — estética

---

## P1

* [ ] recorrência básica de agendamento
* [ ] bloqueio de horários e férias
* [ ] recibo em PDF
* [ ] notificações internas no painel (in-app)
* [ ] consentimentos LGPD na primeira interação
* [ ] wizard de onboarding (10 passos)
* [ ] checklist pós-wizard com celebração
* [ ] base de conhecimento inicial
* [ ] tickets de suporte via email + WhatsApp
* [ ] SLA documentado por plano
* [ ] preferências de notificação por canal
* [ ] gráfico de evolução em antropometria
* [ ] envio do plano alimentar via WhatsApp/portal
* [ ] orientações pós-procedimento automatizadas (dermato/estética)

---

# 5. FASE 1.5 — POLIMENTO

## P1

* [ ] otimização de queries detectadas em produção
* [ ] refino de UX baseado em feedback do piloto
* [ ] melhorias na agenda (visualização compacta)
* [ ] busca global no painel (Cmd+K)
* [ ] atalhos de teclado nas telas críticas
* [ ] empty states refinados
* [ ] error states refinados
* [ ] skeleton loading nas tabelas
* [ ] templates de email com branding aplicado
* [ ] portal do paciente com cores e logo do tenant
* [ ] templates de WhatsApp com identidade da clínica
* [ ] preview ao vivo de personalização
* [ ] templates de mapeamento salvos (import recorrente)
* [ ] import de agenda histórica
* [ ] export por paciente em PDF (LGPD)

---

## P2

* [ ] dark mode
* [ ] export de dados em Excel (xlsx)
* [ ] export de agenda em iCal
* [ ] reset para padrão Clinix (botão em cada ponto de customização)

---

# 6. FASE 2 — EXPANSÃO

> Nota: o builder de prontuário composable, odontograma, sessões de fisioterapia e sigilo psicológico passaram para o MVP (Fase 1). Esta fase foca em convênios/TISS, multi-unidade, CRM e laboratório.

## P1

* [ ] CRM com pipeline de leads
* [ ] funil de recuperação de faltosos
* [ ] funil de inadimplência
* [ ] motor de automações (backend)
* [ ] editor visual de workflows
* [ ] templates pré-prontos de workflow
* [ ] multi-unidade (com branding por unidade)
* [ ] assinatura digital ICP-Brasil
* [ ] faturamento TISS básico
* [ ] glosas e recursos
* [ ] repasse a profissionais
* [ ] DRE mensal
* [ ] templates customizados de documento (camada 2)
* [ ] domínio próprio do portal do paciente (CNAME + SSL)
* [ ] parsers dedicados de migração (iClinic, Doctoralia, Ninsaúde)
* [ ] import via API REST
* [ ] export self-service completo (botão no painel)
* [ ] sync incremental de dados de origem

---

## P2

* [ ] módulo de estoque (ver `docs/estoque.md`)
* [ ] módulo de convênios + TISS (ver `docs/convenios.md`)
* [ ] orçamentos (odonto, estética)
* [ ] parcelamento de tratamento
* [ ] relatórios avançados
* [ ] export de relatórios em PDF
* [ ] segmentação avançada de pacientes
* [ ] campanhas de WhatsApp
* [ ] CRM completo (funis, leads, automações) — ver `docs/crm.md`
* [ ] webhooks de saída para o cliente
* [ ] SMS para OTP/MFA e fallback crítico
* [ ] PITR via WAL contínuo (backup)
* [ ] simulação trimestral de DR
* [ ] ferramenta dedicada de tickets de suporte
* [ ] chat de suporte in-app
* [ ] auditoria externa de acessibilidade
* [ ] dark mode com contraste validado

---

# 7. FASE 2.5 — LABORATÓRIO

## P0 (quando entrar)

* [ ] modelo de pedido / amostra / resultado / laudo
* [ ] etiquetas e código de barras
* [ ] triagem com leitura de barcode
* [ ] processamento com fila por área
* [ ] SLA por exame
* [ ] validação técnica e clínica
* [ ] assinatura digital de laudo
* [ ] entrega via portal do paciente
* [ ] notificação automática
* [ ] dashboard laboratorial

---

## P1

* [ ] coleta domiciliar
* [ ] integração com convênios para faturamento
* [ ] retificação de laudo com versionamento

---

# 8. FASE 3 — INTELIGÊNCIA

## P1

* [ ] resumo automático de prontuário (Claude API)
* [ ] sugestão de CID-10
* [ ] SOAP assistido por IA
* [ ] previsão de no-show (scikit-learn)
* [ ] previsão de inadimplência
* [ ] assistente operacional (NLP → query estruturada)
* [ ] alertas clínicos inteligentes
* [ ] feedback loop nas sugestões
* [ ] dashboard de aceitação de IA

---

## P2

* [ ] busca semântica em prontuário (pgvector)
* [ ] chatbot do portal do paciente
* [ ] transcrição de áudio para evolução

---

# 9. FASE 4 — MOBILE

## P1

* [ ] App Paciente RN/Expo
* [ ] App Profissional RN/Expo
* [ ] push notifications (FCM)
* [ ] biometria
* [ ] offline básico
* [ ] OTA updates

---

# 10. FASE 5 — PLATAFORMA

## P2

* [ ] API pública documentada
* [ ] SDK em JS/TS
* [ ] webhooks completos
* [ ] marketplace de integrações
* [ ] integração com contabilidade
* [ ] integração com ERPs
* [ ] integração com equipamentos médicos (HL7)

---

# 11. FASE 6 — ENTERPRISE

## P2

* [ ] SSO (SAML, OIDC)
* [ ] tenant dedicado opcional
* [ ] SLA enterprise
* [ ] relatórios consolidados multi-clínica
* [ ] customizações por contrato

---

# 12. IDEIAS (P3)

Para amadurecer no futuro:

* [ ] gamificação para adesão a tratamento
* [ ] integração com wearables
* [ ] análise de prontuário cego para pesquisa (com consentimento)
* [ ] marketplace de profissionais
* [ ] integração com convênios via API direta
* [ ] BI white-label
* [ ] versão para hospitais pequenos

---

# 13. DÉFICIT TÉCNICO

Itens não funcionais a endereçar continuamente:

* [ ] cobertura de testes nos módulos críticos > 80%
* [ ] revisão semestral de dependências (Renovate)
* [ ] revisão semestral de queries lentas
* [ ] revisão semestral de RBAC
* [ ] auditoria interna LGPD anual

---

# 14. PROCESSO

Cada item P0/P1 segue:

* descrição clara
* critérios de aceitação
* estimativa
* sprint alvo
* validação em produção

Quando entra em sprint, move para `tasks/sprint-XX.md`.

Quando concluído, registrado em changelog.
