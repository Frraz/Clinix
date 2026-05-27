# Convênios — Clinix

> Módulo de convênios, planos de saúde, TISS e faturamento de operadoras.

---

# 1. VISÃO GERAL

Convênios são parte importante da receita de muitas clínicas.

Mas têm operação completamente diferente do particular:

* tabelas próprias por procedimento
* validade carteira
* autorização prévia
* envio de guias TISS
* glosas
* recursos
* prazo de repasse
* relacionamento contratual

Este módulo precisa nascer separado e bem estruturado.

---

# 2. OBJETIVOS

Garantir:

* atendimento por convênio com fluxo claro
* validação de carteira e autorização
* envio padronizado de guias TISS
* gestão de glosas
* relatórios financeiros por operadora
* projeção de recebimento por convênio

---

# 3. PRINCÍPIOS

* operação por convênio NUNCA se mistura com particular
* TISS rigorosamente seguido
* glosas tratadas como dado de primeira classe
* recurso a glosa documentado
* relatório financeiro segregado

---

# 4. ENTIDADES

## Principais

* `Insurance` — convênio/operadora
* `InsurancePlan` — plano específico dentro da operadora
* `InsuranceContract` — contrato da clínica com a operadora
* `InsuranceProcedureTable` — tabela de procedimentos negociada
* `PatientInsurance` — vínculo paciente-operadora
* `Authorization` — autorização prévia
* `TissGuide` — guia TISS gerada
* `TissGuideItem` — itens da guia
* `Gloss` — glosa recebida
* `GlossAppeal` — recurso à glosa
* `InsurancePayment` — pagamento recebido da operadora

---

# 5. CADASTRO DE CONVÊNIO

Configuração inicial:

* nome
* CNPJ
* registro ANS
* contatos (operacional, comercial, financeiro)
* contrato (PDF anexo)
* prazo padrão de repasse
* tabelas de procedimento aceitas
* observações operacionais

---

# 6. PACIENTE + CONVÊNIO

Cada paciente pode ter:

* convênio principal
* convênios secundários (raro mas existe)
* dados da carteira:
  * número
  * validade
  * acomodação
  * dependência (titular/dependente)

Validação:

* carteira expira → alerta no atendimento
* dados inválidos → bloqueia atendimento (configurável)

---

# 7. AUTORIZAÇÃO PRÉVIA

Para procedimentos que exigem:

* solicitação registrada
* status:
  * `pending` (aguardando operadora)
  * `approved`
  * `rejected`
  * `expired`
* prazo da operadora
* número de autorização registrado
* limite de uso (ex: 10 sessões de fisio)

Sem autorização válida, procedimento não pode ser executado.

---

# 8. TABELAS DE PROCEDIMENTO

Cada convênio negocia valores próprios:

* código do procedimento (TUSS)
* descrição
* valor pago pela operadora
* requer autorização?
* observações

Tabelas atualizáveis (com versionamento).

---

# 9. ATENDIMENTO POR CONVÊNIO

Durante o atendimento:

* sistema identifica convênio do paciente
* aplica tabela negociada
* sugere apenas procedimentos cobertos
* destaca itens não cobertos (cobrança particular complementar)
* registra autorização usada

---

# 10. GUIAS TISS

Gerar guia TISS após atendimento:

* preenchimento automático com dados do paciente, profissional, procedimento
* validação de campos obrigatórios
* geração de XML conforme padrão ANS
* assinatura digital se exigida
* envio à operadora:
  * por sistema da operadora (manual no MVP)
  * via API (quando disponível — Fase 2+)
  * por email (fallback)
* persistência do XML para auditoria

---

# 11. TIPOS DE GUIA

Padrão ANS:

* SADT (serviços auxiliares)
* consulta
* internação (fora do escopo do Clinix por ora)
* anexo de hospital-dia
* honorários
* OPME (próteses)

MVP cobre: consulta e SADT.

---

# 12. STATUS DE GUIA

* `draft` — preenchida, não enviada
* `submitted` — enviada à operadora
* `processing` — sendo processada
* `approved` — aprovada
* `partially_approved` — parcialmente glosada
* `rejected` — totalmente glosada
* `paid` — paga

---

# 13. GLOSAS

Quando operadora glosa item:

* registrar valor glosado
* motivo da glosa (código + descrição)
* item específico afetado
* prazo de recurso

Dashboard de glosas:

* total glosado no mês
* por operadora
* por motivo
* por profissional

---

# 14. RECURSO À GLOSA

Fluxo:

* clínica analisa motivo
* anexa justificativa + documentos
* envia recurso à operadora
* registra status:
  * `submitted`
  * `accepted`
  * `rejected`
* timeline de recurso

---

# 15. REPASSE FINANCEIRO

Operadora paga:

* prazo médio: 30-60 dias
* sistema acompanha previsão vs. recebido
* alerta de atraso configurável
* baixa automática quando bate valor esperado
* divergência aciona análise manual

---

# 16. DASHBOARD POR CONVÊNIO

KPIs essenciais:

* receita prevista
* receita recebida
* taxa de glosa
* prazo médio de recebimento
* procedimentos mais glosados
* aging de recebimento

---

# 17. RELATÓRIOS

Periódicos:

* faturamento mensal por convênio
* glosas do mês
* recursos pendentes
* aging de recebimento
* DRE específico por convênio

---

# 18. INTEGRAÇÃO COM AGENDA

* convênio aceito por profissional/unidade
* agenda destaca pacientes de convênio
* alerta para autorização pendente
* bloqueio se carteira expirada (configurável)

---

# 19. INTEGRAÇÃO COM FINANCEIRO

Cobrança de convênio:

* NÃO vai para o paciente
* vai para `accounts_receivable_insurance` (entidade separada)
* aging próprio
* baixa diferente do particular

Ver `examples/fluxo-financeiro.md`.

---

# 20. INTEGRAÇÃO COM PRONTUÁRIO

Procedimento realizado:

* registrado no prontuário
* vinculado à guia TISS
* CID obrigatório para convênio
* observação de cobertura

---

# 21. COMPLEMENTAR (PARTICULAR + CONVÊNIO)

Quando convênio não cobre tudo:

* parte coberta vai para convênio (TISS)
* diferença vai como cobrança particular ao paciente
* documentado com clareza
* paciente assina autorização

---

# 22. ATENDIMENTO PARTICULAR PARA PACIENTE DE CONVÊNIO

* paciente do convênio decide pagar particular
* registrado explicitamente
* não gera guia TISS
* cobra normalmente como particular

---

# 23. PERFORMANCE

* listagens com paginação
* aging com cache
* envio de TISS assíncrono
* dashboards com refresh inteligente

---

# 24. SEGURANÇA

* dados de convênio sensíveis (LGPD)
* RBAC restrito (financeiro, gestor)
* audit de envio TISS
* assinatura de XML quando exigida
* nunca expor convênio entre tenants

---

# 25. PERMISSÕES

| Ação | Quem |
|------|------|
| Cadastrar convênio | admin |
| Negociar tabelas | admin, gestor |
| Solicitar autorização | recepcionista, profissional |
| Gerar guia TISS | profissional, financeiro |
| Tratar glosas | financeiro |
| Recorrer glosa | financeiro, gestor |
| Visualizar relatórios | gestor, financeiro |

---

# 26. AUDITORIA

Registrar:

* alteração em contrato
* alteração em tabela negociada
* solicitação de autorização
* envio de guia
* recebimento de glosa
* recurso interposto
* baixa de pagamento

---

# 27. ROADMAP

## Fase 1 (MVP)

* não incluído

---

## Fase 2

* cadastro de convênios
* paciente com convênio
* tabelas de procedimento básicas
* geração de guias TISS (XML)
* envio manual à operadora
* registro de glosa básico
* dashboard simples

---

## Fase 2.5

* recurso a glosa formalizado
* relatórios completos
* aging detalhado
* integração com convênios via API (top 10)
* autorização prévia automatizada

---

## Fase 3

* IA prediz glosa antes do envio
* sugestão de correções automáticas
* análise de padrão de glosa por procedimento
* benchmarking entre convênios

---

# 28. COMPLEXIDADE OPERACIONAL

Convênio é um dos módulos mais complexos do produto.

Decisões importantes:

* MVP NÃO inclui (foco em particular)
* Fase 2 começa com TISS XML manual (envio fora do sistema)
* integração com operadoras grandes só quando demanda real existir
* parceria com consultoria de saúde suplementar pode acelerar

---

# 29. O QUE EVITAR

Nunca permitir:

* misturar particular e convênio em mesma cobrança sem segregação
* enviar TISS sem validação
* baixa automática sem conciliação
* glosa sem motivo registrado
* envio de XML sem auditoria
* exposição de tabelas entre tenants

---

# 30. OBJETIVO FINAL

Construir um módulo de convênios que:

* atenda padrão TISS rigorosamente
* dê visibilidade real do faturamento por operadora
* reduza glosa pela validação prévia
* acelera recurso quando ocorrer
* permita ao gestor decidir quais convênios manter
