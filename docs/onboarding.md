# Onboarding — Clinix

> Estratégia de onboarding de novas clínicas no Clinix.

---

# 1. VISÃO GERAL

Onboarding é o ponto que separa:

* clínica que vira cliente fiel
* clínica que cancela em 30 dias

O Clinix deve transformar o primeiro contato em produtividade real o mais rápido possível.

Onboarding ruim = churn alto = MRR perdido.

---

# 2. OBJETIVOS

Garantir:

* clínica operacional em até 7 dias
* time da clínica treinado
* dados migrados (quando aplicável)
* configurações personalizadas
* primeiros pacientes atendidos no sistema dentro da 1ª semana
* relacionamento direto estabelecido com o cliente

---

# 3. PRINCÍPIOS

O onboarding deve:

* ser guiado (não esperar que o cliente descubra)
* ter padrões fortes (clínica não precisa configurar tudo)
* ser interruptivelmente progressivo (cada passo entrega valor)
* ser humano quando necessário
* gerar dados para melhorar o produto

---

# 4. ETAPAS

## Etapa 1 — Venda

Pré-onboarding:

* demo do produto
* proposta comercial
* expectativas alinhadas
* identificação de:
  * sistema atual (se houver)
  * volume de pacientes
  * número de profissionais
  * urgência

---

## Etapa 2 — Pagamento e provisionamento

* assinatura contratada
* taxa de implantação paga (ver `docs/billing.md`)
* tenant provisionado automaticamente
* subdomínio criado
* admin convidado por email

---

## Etapa 3 — Setup inicial (wizard)

Wizard guiado no primeiro login:

1. dados da clínica (CNPJ, endereço, contatos)
2. responsável técnico (CRM, CRO, etc.)
3. upload de logo
4. cor primária
5. especialidades atendidas
6. tipos de consulta
7. horário de funcionamento
8. convite a profissionais
9. convite a recepcionistas
10. preferências básicas (WhatsApp, lembretes)

---

## Etapa 4 — Migração de dados

Quando aplicável:

* cliente envia export do sistema antigo
* parser dedicado processa (Fase 2) OU
* mapeamento CSV manual com suporte
* validação e import
* clínica revisa amostra

Detalhamento em `docs/importacao-exportacao.md`.

---

## Etapa 5 — Treinamento

* sessão online ao vivo (incluso na taxa de implantação)
* gravação disponibilizada
* base de conhecimento liberada
* tour interativo no sistema
* materiais por perfil (admin, médico, recepcionista, financeiro)

---

## Etapa 6 — Ativação

Clínica começa a operar:

* primeiros agendamentos reais
* WhatsApp ativado
* primeiros atendimentos
* primeiras cobranças

Time de sucesso acompanha de perto.

---

## Etapa 7 — Acompanhamento

Primeiros 90 dias:

* contato semanal (Sucesso do Cliente)
* identificação de bloqueios
* iteração rápida com produto
* coleta de feedback estruturada

---

# 5. PERFIS NO ONBOARDING

## Admin / gestor

* recebe convite inicial
* completa o wizard
* convida demais usuários
* faz parametrizações

---

## Médico / profissional clínico

* recebe convite via email
* aceita
* configura senha + MFA
* completa perfil (especialidade, registro)
* explora prontuário
* recebe treinamento dedicado

---

## Recepcionista

* recebe convite
* treinamento focado em:
  * agenda
  * cadastro de pacientes
  * check-in
  * cobrança rápida

---

## Financeiro

* treinamento focado em:
  * cobranças
  * fluxo de caixa
  * relatórios
  * conciliação

---

# 6. WIZARD DE SETUP

## UX do wizard

* progresso visível (1/10, 2/10...)
* "pular por ora" disponível na maioria dos passos
* dados pré-preenchidos quando possível (CNPJ → busca na Receita)
* preview imediato (logo + cores aplicados ao vivo)
* salvamento automático
* retomável (admin pode pausar e voltar)

---

## Estado do wizard

* `not_started`
* `in_progress`
* `completed`
* `skipped`

Dashboard mostra etapas pendentes mesmo após sair do wizard.

---

# 7. CHECKLIST PÓS-WIZARD

Visível no painel:

* [ ] convidar primeiro profissional
* [ ] cadastrar primeira especialidade
* [ ] configurar tipos de consulta
* [ ] ativar WhatsApp
* [ ] cadastrar primeiro paciente
* [ ] marcar primeiro agendamento
* [ ] realizar primeiro atendimento
* [ ] emitir primeira receita
* [ ] gerar primeira cobrança
* [ ] receber primeiro pagamento

Cada item completado libera celebração discreta.

---

# 8. MATERIAIS DE TREINAMENTO

## Base de conhecimento

* artigos por módulo
* vídeos curtos (< 3 min)
* tutoriais interativos
* FAQ

---

## Por perfil

Trilhas específicas:

* "Primeiros passos para administradores"
* "Como o médico usa o Clinix"
* "Recepção em 30 minutos"
* "Financeiro do zero"

---

## Sessões ao vivo

* webinars mensais
* office hours quinzenais
* treinamento dedicado (incluso na implantação)

---

# 9. MIGRAÇÃO DE DADOS

Detalhamento em `docs/importacao-exportacao.md`.

Resumo do processo:

1. cliente identifica sistema de origem
2. cliente extrai dados (ou recebe ajuda do Clinix)
3. CSV revisado pelo time de Sucesso
4. mapeamento confirmado
5. import dry-run
6. revisão da amostra com o cliente
7. import definitivo
8. validação no sistema
9. liberação para operação

---

# 10. PRAZOS PADRÃO

| Etapa | Duração esperada |
|-------|------------------|
| Provisionamento | < 5 minutos |
| Wizard | 30-60 minutos |
| Migração simples | 1-2 dias |
| Migração complexa | 3-7 dias |
| Treinamento básico | 1 sessão de 1h |
| Treinamento avançado | 2-3 sessões |
| Time-to-first-value | < 7 dias |

---

# 11. SUCESSO DO CLIENTE

Time dedicado durante os primeiros 90 dias:

* contato proativo semanal
* identifica bloqueios
* coleta feedback estruturado
* escala para suporte/produto
* monitora indicadores de adoção
* previne churn

---

# 12. MÉTRICAS DE ONBOARDING

KPIs essenciais:

* tempo médio de provisionamento → ativação
* taxa de conclusão do wizard
* tempo até primeiro atendimento real
* % de checklist concluído em 7/30/90 dias
* NPS após 30 dias
* taxa de cancelamento em 90 dias
* tickets de suporte por cliente novo

---

# 13. SINAIS DE ALERTA

Disparam intervenção do Sucesso:

* wizard não concluído em 48h
* nenhum login em 72h após ativação
* nenhum atendimento real em 14 dias
* tickets de suporte recorrentes sobre o mesmo tema
* NPS < 7 após 30 dias

---

# 14. SELF-SERVICE VS ASSISTIDO

## Self-service

* setup inicial padrão
* migração pequena (< 500 pacientes)
* treinamento via base de conhecimento

Para clientes Starter.

---

## Assistido

* setup acompanhado
* migração complexa
* treinamento ao vivo dedicado
* implantação presencial (sob orçamento)

Para clientes Professional+.

---

# 15. AUTOMAÇÕES INTERNAS

Eventos de onboarding disparam:

* email de boas-vindas
* email com link de acesso à base de conhecimento
* convite para webinar de boas-vindas
* notificação interna para Sucesso ao concluir wizard
* alerta a Sucesso se cliente parar de progredir
* email de marco (primeiro atendimento, primeira cobrança paga)

---

# 16. PERSONALIZAÇÃO NO ONBOARDING

Durante o wizard:

* clínica já sai com branding aplicado
* logo no header
* cor primária no sistema
* templates de documento já assinados
* portal do paciente já personalizado

Ver `docs/personalizacao.md`.

---

# 17. INTEGRAÇÕES ATIVADAS

No onboarding inicial:

* WhatsApp (Evolution API ou WhatsApp Business)
* Mercado Pago (financeiro)
* Resend (email transacional)

Configuração simplificada via wizard.

---

# 18. EXPECTATIVAS ALINHADAS

O cliente deve sair do onboarding sabendo:

* o que o Clinix entrega (e o que NÃO entrega)
* qual o suporte disponível por plano
* como solicitar nova feature
* política de uptime e incidentes
* limites do plano (usuários, WhatsApp, storage)
* política de cancelamento e portabilidade

---

# 19. DOCUMENTOS LEGAIS

Aceitos no onboarding:

* contrato de prestação de serviços (SaaS)
* termo de tratamento de dados (LGPD)
* termo de uso
* política de privacidade
* matriz de responsabilidades operador/controlador

Versionados e auditados.

---

# 20. ROADMAP DE ONBOARDING

## Fase 1 (MVP)

* wizard básico
* provisionamento manual
* treinamento ao vivo
* checklist pós-wizard

---

## Fase 1.5

* base de conhecimento estruturada
* tour interativo
* automações de email

---

## Fase 2

* parsers dedicados de migração
* sucesso do cliente proativo
* dashboard interno de onboarding
* alertas automáticos para Sucesso

---

## Fase 3

* IA sugere próximas configurações
* migração assistida por IA
* personalização sugerida a partir do logo
* benchmarks comparativos

---

# 21. O QUE EVITAR

Nunca permitir:

* deixar cliente sozinho após pagamento
* migração sem revisão humana
* treinamento engessado (cada clínica é diferente)
* onboarding genérico ignorando especialidade
* prometer features que não temos
* cancelamento por bloqueio técnico não resolvido

---

# 22. OBJETIVO FINAL

Construir um onboarding que:

* leva a clínica do "comprou" ao "operando" em até 7 dias
* reduz churn nos primeiros 90 dias
* estabelece relacionamento direto
* gera promotores
* aprende continuamente com cada novo cliente
