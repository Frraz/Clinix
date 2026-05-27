# Regras de Negócio — Clinix

> Regras operacionais, comportamentais e funcionais centrais do sistema Clinix.

---

# 1. VISÃO GERAL

O Clinix é uma plataforma operacional para clínicas, laboratórios e operações de saúde.

Todas as regras de negócio devem priorizar:

* segurança
* rastreabilidade
* produtividade
* automação
* simplicidade operacional
* multi-tenancy
* escalabilidade
* experiência do usuário

---

# 2. MULTI-TENANCY

## Regra Principal

Toda informação pertence obrigatoriamente a uma clínica (tenant).

---

## Regras obrigatórias

* todo registro deve possuir vínculo com tenant
* nenhum dado pode ser acessado entre clínicas
* toda query deve considerar tenant
* toda API deve validar tenant
* permissões devem ser contextualizadas por tenant

---

## Falhas críticas

São consideradas falhas críticas:

* vazamento de dados entre clínicas
* acesso indevido entre tenants
* queries sem isolamento
* endpoints sem validação de tenant

---

# 3. USUÁRIOS E PERMISSÕES

## Tipos de usuários

O sistema deve suportar:

* administradores
* gestores
* recepcionistas
* médicos
* dentistas
* psicólogos
* fisioterapeutas
* biomédicos
* técnicos laboratoriais
* financeiro
* suporte

---

## Regras de permissão

O sistema deve possuir RBAC granular.

Permissões devem considerar:

* tenant
* unidade
* módulo
* ação
* função
* contexto operacional

---

## Exemplos de permissões

* visualizar pacientes
* editar prontuário
* cancelar consultas
* acessar financeiro
* emitir laudos
* visualizar relatórios
* alterar permissões

---

# 4. PACIENTES

## Cadastro de pacientes

Todo paciente deve possuir:

* identificação única
* vínculo com tenant
* histórico completo
* rastreabilidade
* dados de contato
* dados clínicos relevantes

---

## Regras importantes

* CPF deve evitar duplicidade quando aplicável
* histórico nunca deve ser perdido
* alterações importantes devem gerar auditoria
* dados sensíveis devem possuir controle de acesso

---

## Situações possíveis

Paciente pode estar:

* ativo
* inativo
* bloqueado
* arquivado

---

# 5. AGENDA E AGENDAMENTOS

## Regras da agenda

Toda consulta/agendamento deve possuir:

* paciente
* profissional
* unidade
* horário
* status
* tenant

---

## Status possíveis

* agendado
* confirmado
* aguardando
* em atendimento
* concluído
* cancelado
* faltou
* reagendado

---

## Regras obrigatórias

* evitar conflitos de agenda
* validar disponibilidade
* registrar alterações
* registrar cancelamentos
* registrar reagendamentos

---

## Regras de cancelamento

Todo cancelamento deve:

* registrar responsável
* registrar motivo
* gerar auditoria
* atualizar agenda

---

## Regras de no-show

Pacientes faltosos devem:

* ser registrados
* gerar métricas
* permitir automações futuras
* alimentar relatórios operacionais

---

# 6. CONFIRMAÇÃO DE CONSULTAS

## O sistema deve permitir:

* confirmação automática
* confirmação manual
* lembretes automáticos
* envio via WhatsApp
* envio via e-mail

---

## Regras

* mensagens devem possuir histórico
* status de entrega deve ser rastreado
* respostas futuras poderão alimentar automações

---

# 7. PRONTUÁRIO

## Regras gerais

O prontuário deve ser:

* seguro
* rastreável
* auditável
* organizado
* cronológico

---

## Toda alteração deve registrar

* usuário
* data
* horário
* tenant
* alteração realizada

---

## O prontuário deve suportar

* anamnese
* evolução
* anexos
* exames
* prescrições
* laudos
* documentos
* imagens

---

## Regras críticas

* histórico nunca deve ser perdido
* alterações críticas devem ser auditáveis
* permissões devem ser rigorosas
* dados sensíveis devem possuir proteção adicional

---

# 8. DOCUMENTOS E ANEXOS

## O sistema deve permitir

* upload de documentos
* upload de exames
* upload de imagens
* categorização
* rastreamento

---

## Regras obrigatórias

* arquivos devem possuir tenant
* arquivos devem possuir auditoria
* uploads devem ser seguros
* permissões devem ser respeitadas

---

# 9. FINANCEIRO

## O módulo financeiro deve suportar

* contas a pagar
* contas a receber
* faturamento
* fluxo de caixa
* parcelamentos
* inadimplência
* pagamentos
* repasses
* comissões

---

## Regras obrigatórias

* movimentações devem ser rastreáveis
* exclusões financeiras devem gerar auditoria
* pagamentos devem possuir histórico
* parcelamentos devem manter integridade

---

## Status financeiros

Exemplos:

* pendente
* pago
* vencido
* cancelado
* parcelado
* parcialmente pago

---

# 10. COBRANÇAS E INADIMPLÊNCIA

## O sistema deve permitir

* geração de cobranças
* lembretes automáticos
* acompanhamento financeiro
* automações de cobrança

---

## Regras

* cobranças devem possuir histórico
* ações automáticas devem ser registradas
* automações devem respeitar configurações da clínica

---

# 11. FATURAMENTO

## O sistema deve permitir

* emissão de cobranças
* emissão de recibos
* emissão de comprovantes
* controle financeiro operacional

---

## Regras

* toda cobrança deve possuir rastreabilidade
* histórico financeiro nunca deve ser perdido
* alterações devem gerar auditoria

---

# 12. LABORATÓRIO

## O módulo laboratorial deve suportar

* pedidos de exames
* processamento de exames
* status laboratoriais
* emissão de laudos
* anexos
* integração operacional

---

## Status possíveis

* solicitado
* coletado
* em análise
* concluído
* liberado
* cancelado

---

## Regras obrigatórias

* exames devem possuir rastreabilidade
* laudos devem possuir histórico
* alterações devem ser auditáveis

---

# 13. COMUNICAÇÃO

## O sistema deve centralizar comunicação operacional.

---

## Canais suportados futuramente

* WhatsApp
* e-mail
* SMS
* push notifications

---

## Toda comunicação deve possuir

* histórico
* rastreamento
* status
* vínculo com paciente
* vínculo com tenant

---

# 14. WHATSAPP

## WhatsApp é parte central do produto.

---

## O sistema deve suportar

* mensagens automáticas
* templates
* confirmações
* follow-up
* campanhas
* notificações

---

## Regras

* mensagens devem possuir histórico
* falhas devem ser rastreadas
* automações devem ser configuráveis
* filas devem evitar perda de mensagens

---

# 15. CRM E RELACIONAMENTO

## O sistema deve permitir

* acompanhamento de pacientes
* follow-up
* campanhas
* recuperação de pacientes
* histórico de relacionamento

---

## Objetivos

* retenção
* relacionamento
* aumento de retorno
* redução de faltas

---

# 16. AUDITORIA

## Toda ação crítica deve gerar auditoria.

---

## Exemplos

* login
* alterações financeiras
* alterações clínicas
* alterações de permissões
* exclusões
* cancelamentos

---

## Logs devem possuir

* usuário
* tenant
* ação
* entidade afetada
* timestamp
* IP quando aplicável

---

# 17. EVENTOS E AUTOMAÇÕES

## O sistema deve ser preparado para eventos.

---

## Exemplos de eventos

* consulta criada
* consulta cancelada
* pagamento realizado
* exame liberado
* paciente cadastrado

---

## Eventos devem permitir

* automações
* notificações
* workflows
* integrações
* analytics
* IA

---

# 18. IA

## A IA deve funcionar como camada auxiliar.

---

## Futuras possibilidades

* resumo clínico
* insights operacionais
* previsão de faltas
* sumarização
* classificação automática
* assistentes internos

---

## Regras

* IA nunca deve substituir validação humana crítica
* informações sensíveis devem respeitar LGPD
* rastreabilidade deve ser mantida

---

# 19. LGPD E DADOS SENSÍVEIS

## O sistema manipula dados altamente sensíveis.

---

## Toda funcionalidade deve considerar

* privacidade
* rastreabilidade
* consentimento
* segurança
* controle de acesso

---

## Regras obrigatórias

* dados sensíveis devem possuir proteção adicional
* acesso deve respeitar permissões
* logs devem registrar alterações críticas

---

# 20. RELATÓRIOS E BI

## O sistema deve permitir

* dashboards
* métricas
* relatórios operacionais
* relatórios financeiros
* indicadores de produtividade

---

## Regras

* relatórios devem respeitar tenant
* relatórios devem respeitar permissões
* métricas devem possuir consistência

---

# 21. MOBILE

## O sistema deve nascer preparado para mobile.

---

## Toda regra deve considerar

* APIs mobile-friendly
* autenticação mobile
* notificações futuras
* experiência operacional mobile

---

# 22. PERFORMANCE

## Toda implementação deve considerar

* performance
* cache
* paginação
* filas
* processamento assíncrono
* escalabilidade

---

## Nunca permitir

* queries extremamente pesadas
* carregamentos desnecessários
* operações bloqueantes em massa

---

# 23. INTEGRAÇÕES

## O sistema deve permitir integrações futuras.

---

## Exemplos futuros

* gateways de pagamento
* APIs externas
* laboratórios
* plataformas de comunicação
* sistemas terceiros

---

## Regras

* integrações devem ser desacopladas
* falhas devem ser rastreáveis
* integrações devem respeitar tenant

---

# 24. SOFT DELETE

## Algumas entidades devem utilizar soft delete.

---

## Exemplos

* pacientes
* documentos
* registros operacionais

---

## Regras

* histórico deve ser preservado
* exclusões críticas devem ser auditáveis

---

# 25. CONSISTÊNCIA OPERACIONAL

Toda funcionalidade deve priorizar:

* clareza
* simplicidade
* produtividade
* baixa carga cognitiva
* experiência operacional rápida

---

# 26. PRINCÍPIOS GERAIS

Toda implementação deve:

* respeitar tenant
* respeitar permissões
* respeitar LGPD
* gerar auditoria quando necessário
* priorizar rastreabilidade
* priorizar performance
* priorizar simplicidade operacional

---

# 27. OBJETIVO DAS REGRAS DE NEGÓCIO

Garantir:

* segurança
* organização
* escalabilidade
* previsibilidade
* rastreabilidade
* integridade operacional
* experiência premium
* fundação sólida para evolução futura
