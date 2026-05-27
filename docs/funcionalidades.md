# Funcionalidades — Clinix

> Mapeamento macro de funcionalidades, módulos e capacidades do sistema Clinix.

---

# 1. VISÃO GERAL

O Clinix será uma plataforma operacional completa para clínicas, laboratórios e operações de saúde.

O sistema deve centralizar:

* operação clínica
* comunicação
* financeiro
* automações
* prontuários
* relacionamento com pacientes
* gestão operacional
* inteligência de negócio
* workflows
* integrações

---

# 2. MÓDULOS PRINCIPAIS

O Clinix será dividido em módulos.

---

# 3. DASHBOARD OPERACIONAL

## Objetivo

Centralizar visão operacional da clínica.

---

## Funcionalidades

* visão geral da operação
* consultas do dia
* métricas rápidas
* pacientes aguardando
* indicadores financeiros
* tarefas pendentes
* alertas operacionais
* notificações
* acessos rápidos
* KPIs operacionais

---

# 4. GESTÃO DE PACIENTES

## Objetivo

Centralizar dados e histórico dos pacientes.

---

## Funcionalidades

* cadastro de pacientes
* busca avançada
* histórico completo
* timeline do paciente
* documentos
* anexos
* contatos
* convênios
* responsáveis
* observações
* status do paciente
* tags
* filtros avançados
* histórico de atendimentos
* histórico financeiro
* histórico de comunicação

---

# 5. AGENDA E AGENDAMENTOS

## Objetivo

Gerenciar agenda operacional da clínica.

---

## Funcionalidades

* agenda diária
* agenda semanal
* agenda mensal
* múltiplos profissionais
* múltiplas unidades
* encaixes
* reagendamentos
* cancelamentos
* confirmação de consultas
* controle de disponibilidade
* bloqueios de agenda
* recorrência
* fila de espera
* agenda por sala
* agenda por equipamento
* drag and drop
* filtros avançados
* visualização por profissional
* visualização por unidade
* status operacionais
* alertas de conflito

---

# 6. CHECK-IN E RECEPÇÃO

## Funcionalidades

* check-in de pacientes
* fila de espera
* pacientes aguardando
* status operacional
* chamada de pacientes
* confirmação de chegada
* recepção operacional
* gestão de atendimento em andamento

---

# 7. PRONTUÁRIO ELETRÔNICO

## Objetivo

Gerenciar informações clínicas e histórico do paciente em uma engine composable que serve as 8 especialidades-alvo.

---

## Funcionalidades

* anamnese (estruturada por template de especialidade)
* evolução clínica (SOAP ou template específico)
* histórico médico
* prescrições (sistêmica, tópica, dietética, fisioterapêutica)
* documentos
* anexos
* imagens clínicas (com consentimento e antes/depois)
* exames
* laudos
* SOAP
* CID
* timeline clínica unificada
* templates clínicos versionados por especialidade
* anotações privadas (psicologia) com sigilo reforçado
* builder dinâmico de campos
* assinatura digital simples (MVP) → ICP-Brasil (Fase 2)
* upload de arquivos no R2
* histórico completo
* auditoria de alterações (e leitura em dados sensíveis)
* controle de acesso por especialidade

Detalhamento: `docs/prontuario.md`, `docs/templates-clinicos.md`.

---

# 7.1. ESPECIALIDADES-ALVO

O Clinix atende **8 especialidades-alvo** desde o MVP, todas via prontuário composable + componentes específicos quando necessário.

---

## Clínicas médicas gerais

* SOAP padrão
* CID-10 com autocomplete
* prescrições sistêmicas
* atestados
* solicitação de exames
* timeline clínica

---

## Odontologia

* odontograma com notação FDI
* plano de tratamento com procedimentos
* orçamento e parcelamento
* histórico de procedimentos por dente
* imagens odontológicas

Detalhamento: `docs/odontograma.md`, `docs/pacotes-tratamento.md`.

---

## Fisioterapia

* avaliação funcional (ROM, força, dor)
* plano de tratamento com N sessões
* evolução por sessão
* exercícios recomendados
* alta clínica

Detalhamento: `docs/pacotes-tratamento.md`.

---

## Psicologia

* escalas validadas: PHQ-9, GAD-7, DASS-21, BDI
* aplicação digital com cálculo de score
* gráfico de evolução por escala
* anotações privadas com sigilo reforçado (`is_private`)
* alerta automático em score de risco

Detalhamento: `docs/escalas-testes.md`.

---

## Biomedicina / Laboratórios

* solicitação de exames
* upload de laudo externo (MVP)
* entrega via portal e WhatsApp
* operação interna completa (coleta, triagem, processamento, validação técnica) na Fase 2.5

Detalhamento: `docs/laboratorio.md`.

---

## Dermatologia

* anamnese dermatológica
* mapa corporal de lesões (SVG)
* dermatoscopia com anotações
* fotos clínicas com metadados
* descrição padronizada de lesão
* prescrição tópica
* protocolo de seguimento

Detalhamento: `docs/dermatologia.md`, `docs/imagens-clinicas.md`.

---

## Clínicas de Estética

* anamnese estética
* checklist de contraindicações por procedimento
* pacotes de tratamento com N sessões
* fotos antes/depois com slider de comparação
* orçamento e parcelamento por pacote
* termo de consentimento por procedimento
* lembrete de manutenção

Detalhamento: `docs/estetica.md`, `docs/pacotes-tratamento.md`, `docs/imagens-clinicas.md`.

---

## Nutrição

* anamnese nutricional
* antropometria (peso, altura, IMC, circunferências, dobras, % gordura)
* bioimpedância
* plano alimentar versionado
* recordatório alimentar
* envio do plano via WhatsApp/portal
* gráfico de evolução

Detalhamento: `docs/nutricao.md`, `docs/antropometria.md`.

---

# 8. DOCUMENTOS E ARQUIVOS

## Funcionalidades

* upload de documentos
* upload de exames
* upload de imagens
* categorização
* preview
* download
* controle de permissões
* organização por paciente
* organização por atendimento
* rastreabilidade

---

# 9. FINANCEIRO

## Objetivo

Centralizar operação financeira da clínica.

---

## Funcionalidades

* contas a pagar
* contas a receber
* fluxo de caixa
* faturamento
* parcelamentos
* inadimplência
* repasses
* comissões
* recebimentos
* pagamentos
* categorias financeiras
* centros de custo
* relatórios financeiros
* histórico financeiro
* controle de vencimentos
* controle de cobranças
* status financeiros
* dashboard financeiro
* movimentações
* auditoria financeira

---

# 10. FATURAMENTO

## Funcionalidades

* geração de cobranças
* emissão de recibos
* comprovantes
* histórico de pagamentos
* controle de parcelamentos
* gestão de inadimplência
* rastreamento financeiro

---

# 11. CRM E RELACIONAMENTO

## Objetivo

Gerenciar relacionamento com pacientes.

---

## Funcionalidades

* follow-up
* recuperação de pacientes
* campanhas
* histórico de comunicação
* gestão de relacionamento
* observações comerciais
* automações de relacionamento
* segmentação de pacientes
* lembretes
* fidelização

---

# 12. WHATSAPP

## Objetivo

Centralizar comunicação operacional.

---

## Funcionalidades

* integração WhatsApp
* envio automático de mensagens
* lembretes automáticos
* confirmação de consultas
* follow-up automático
* notificações
* templates
* campanhas
* histórico de mensagens
* rastreamento de mensagens
* filas de envio
* automações
* comunicação operacional
* mensagens personalizadas

---

# 13. E-MAIL E COMUNICAÇÃO

## Funcionalidades

* envio de e-mails
* notificações automáticas
* templates
* campanhas
* comunicação operacional
* histórico de envio
* automações multicanal

---

# 14. LABORATÓRIO

## Objetivo

Gerenciar operação laboratorial.

---

## Funcionalidades

* pedidos de exames
* controle laboratorial
* status de exames
* coleta
* processamento
* emissão de laudos
* anexos laboratoriais
* rastreamento
* histórico de exames
* integração operacional
* timeline laboratorial

---

# 15. ESTOQUE

## Funcionalidades

* controle de estoque
* entrada e saída
* movimentações
* alertas de baixo estoque
* histórico
* rastreabilidade
* categorias
* fornecedores
* relatórios

---

# 16. CONVÊNIOS

## Funcionalidades

* cadastro de convênios
* tabelas de preços
* regras de atendimento
* controle financeiro
* relacionamento com convênios

---

# 17. PROFISSIONAIS

## Funcionalidades

* cadastro de profissionais
* especialidades
* agenda
* permissões
* unidades
* comissões
* produtividade
* documentos
* horários
* disponibilidade

---

# 18. UNIDADES E FILIAIS

## Funcionalidades

* múltiplas unidades
* gestão multiunidade
* permissões por unidade
* agenda por unidade
* financeiro por unidade
* relatórios por unidade
* dashboards separados

---

# 19. MULTI-TENANCY

## Funcionalidades

* isolamento entre clínicas
* gestão de tenants
* permissões contextualizadas
* configurações por tenant
* billing por tenant

---

# 20. AUTH E SEGURANÇA

## Funcionalidades

* login seguro
* JWT
* sessões
* recuperação de senha
* RBAC
* permissões granulares
* auditoria
* controle de acesso
* autenticação futura MFA
* logs de acesso

---

# 21. AUDITORIA

## Funcionalidades

* logs operacionais
* logs financeiros
* logs clínicos
* rastreamento de alterações
* histórico de ações
* auditoria por usuário
* auditoria por tenant

---

# 22. RELATÓRIOS

## Funcionalidades

* relatórios operacionais
* relatórios financeiros
* relatórios clínicos
* métricas
* produtividade
* exportação
* filtros avançados
* dashboards
* KPIs

---

# 23. BUSINESS INTELLIGENCE

## Funcionalidades futuras

* dashboards inteligentes
* analytics
* indicadores operacionais
* métricas financeiras
* métricas clínicas
* análise de produtividade
* previsão de resultados

---

# 24. IA

## Objetivo

Adicionar inteligência operacional ao sistema.

---

## Funcionalidades futuras

* resumo clínico automático
* sumarização
* insights operacionais
* classificação automática
* previsão de faltas
* assistente interno
* análise operacional
* geração assistida
* automações inteligentes

---

# 25. AUTOMAÇÕES

## Funcionalidades

* workflows automáticos
* lembretes
* follow-ups
* notificações
* cobranças automáticas
* mensagens automáticas
* recuperação de pacientes
* automações operacionais
* filas de execução

---

# 26. EVENTOS E WORKFLOWS

## Funcionalidades

* eventos do sistema
* workflows configuráveis
* gatilhos automáticos
* ações automáticas
* processamento assíncrono
* integração entre módulos

---

# 27. TELEMEDICINA

## Funcionalidades futuras

* consultas online
* videochamadas
* integração operacional
* prontuário integrado
* agendamento remoto
* notificações

---

# 28. PORTAL DO PACIENTE

## Funcionalidades futuras

* acesso do paciente
* histórico de consultas
* exames
* documentos
* pagamentos
* agendamentos
* comunicação
* notificações

---

# 29. MOBILE

## Objetivo

Expandir experiência operacional para dispositivos móveis.

---

## Funcionalidades futuras

* aplicativo Android
* aplicativo iOS
* notificações push
* agenda mobile
* prontuário mobile
* comunicação mobile
* dashboards mobile

---

# 30. CONFIGURAÇÕES DO SISTEMA

## Funcionalidades

* configurações da clínica
* personalizações
* usuários
* permissões
* automações
* templates
* integrações
* notificações
* parâmetros operacionais

---

# 30.1. PERSONALIZAÇÃO POR CLÍNICA

## Objetivo

Permitir que cada clínica reflita sua identidade no sistema.

---

## Funcionalidades

* upload de logo (principal e monocromática)
* cor primária configurável (com validação de contraste)
* dados completos da clínica (CNPJ, endereço, contatos, responsável técnico)
* templates de documento personalizados (receitas, atestados, laudos, recibos, contratos)
* templates de WhatsApp e email com identidade da clínica
* portal do paciente com cores e logo da clínica
* domínio próprio (Business+)
* branding por unidade em operações multiunidade
* preview ao vivo antes de aplicar
* reset para padrão Clinix a qualquer momento

Detalhamento em `docs/personalizacao.md`.

---

# 30.2. IMPORTAÇÃO E EXPORTAÇÃO DE DADOS

## Objetivo

Garantir portabilidade total: onboarding rápido de clínicas vindas de outros sistemas, e saída sem aprisionamento.

---

## Funcionalidades de importação

* import de pacientes via CSV/Excel
* import de profissionais
* import de agenda histórica
* import de contas a receber em aberto
* import de documentos e anexos (ZIP estruturado)
* mapeamento visual de colunas
* templates de mapeamento reutilizáveis
* preview antes de aplicar
* validação por linha + relatório de erros
* deduplicação inteligente (merge, skip, overwrite, create_anyway)
* parsers dedicados para sistemas concorrentes (Fase 2)
* import via API (Fase 2)

---

## Funcionalidades de exportação

* export operacional (CSV, Excel, PDF) por módulo
* export de agenda em iCal
* export de prontuário individual em PDF
* export completo da clínica em ZIP estruturado (offboarding)
* export por paciente (LGPD — art. 18, V)
* jobs assíncronos com notificação ao concluir
* URLs assinadas com TTL
* schema versionado e documentado publicamente

Detalhamento em `docs/importacao-exportacao.md`.

---

# 31. DASHBOARDS E KPIs

## Funcionalidades

* indicadores financeiros
* indicadores operacionais
* produtividade
* faturamento
* no-show
* ocupação de agenda
* crescimento operacional
* métricas estratégicas

---

# 32. INTEGRAÇÕES FUTURAS

## Possíveis integrações

* gateways de pagamento
* APIs externas
* laboratórios
* sistemas terceiros
* ERPs
* plataformas de comunicação
* ferramentas de BI

---

# 33. API PÚBLICA

## Funcionalidades futuras

* APIs externas
* webhooks
* integrações
* autenticação externa
* documentação OpenAPI

---

# 34. NOTIFICAÇÕES

## Funcionalidades

* notificações internas
* alertas operacionais
* lembretes
* push notifications futuras
* notificações financeiras
* notificações clínicas

---

# 35. FILAS E PROCESSAMENTO ASSÍNCRONO

## Funcionalidades

* filas de mensagens
* filas de notificações
* processamento assíncrono
* workers
* automações
* tarefas agendadas

---

# 36. LGPD E PRIVACIDADE

## Funcionalidades

* rastreabilidade
* controle de acesso
* auditoria
* proteção de dados
* consentimento
* segurança operacional

---

# 37. EXPERIÊNCIA OPERACIONAL

## O sistema deve priorizar

* produtividade
* simplicidade
* rapidez
* clareza visual
* baixa carga cognitiva
* experiência premium

---

# 38. FUNCIONALIDADES ENTERPRISE FUTURAS

## Futuras capacidades

* multiunidades avançado
* franquias
* BI avançado
* analytics inteligentes
* automações avançadas
* IA operacional
* integrações enterprise
* alta escalabilidade

---

# 39. ONBOARDING

## Objetivo

Levar a clínica do "comprou" ao "operando" em até 7 dias.

---

## Funcionalidades

* wizard guiado de 10 passos
* preenchimento automático via CNPJ
* convite de profissionais e equipe
* checklist pós-wizard
* base de conhecimento
* tour interativo
* treinamento ao vivo (incluso na implantação)
* sucesso do cliente proativo nos primeiros 90 dias

Detalhamento em `docs/onboarding.md`.

---

# 40. SUPORTE AO CLIENTE

## Funcionalidades

* tickets via email, WhatsApp, in-app
* base de conhecimento pública
* ajuda contextual em cada tela
* SLAs por plano
* CSAT pós-ticket
* status page pública
* comunicação proativa em incidentes
* chat in-app (Fase 2)
* gerente de conta (Enterprise)

Detalhamento em `docs/suporte.md`.

---

# 41. BACKUP E DISASTER RECOVERY

## Funcionalidades

* backup diário automatizado (PostgreSQL → R2)
* backup semanal em provedor independente (B2)
* PITR via WAL (Fase 2)
* testes regulares de restauração
* runbook documentado
* status page em incidentes
* RTO 4h / RPO 24h no MVP, 1h / 15min no Enterprise

Detalhamento em `docs/backup-dr.md`.

---

# 42. ACESSIBILIDADE

## Funcionalidades

* WCAG 2.2 nível AA em todo o produto
* navegação 100% por teclado
* compatibilidade com leitores de tela
* contraste validado
* atalhos de teclado documentados
* tap targets generosos no mobile
* aspiração WCAG AAA no portal do paciente

Detalhamento em `docs/acessibilidade.md`.

---

# 43. PERFORMANCE

## SLOs principais

* API p95 < 500ms
* LCP frontend < 2.5s
* portal do paciente < 2s em 3G
* envio de WhatsApp < 10s
* uptime 99.5% MVP / 99.9% Enterprise

Detalhamento em `docs/performance.md`.

---

# 44. OBJETIVO DAS FUNCIONALIDADES

O Clinix deve evoluir para uma plataforma operacional completa para saúde.

O sistema deve unir:

* gestão
* operação
* comunicação
* financeiro
* automações
* inteligência
* produtividade
* experiência premium
* portabilidade total
* personalização por clínica
* acessibilidade
* performance superior
