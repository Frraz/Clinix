# Estoque — Clinix

> Módulo de controle de estoque para clínicas e laboratórios.

---

# 1. VISÃO GERAL

Estoque é módulo essencial para:

* clínicas com procedimentos (odonto, estética, dermato)
* laboratórios (reagentes, insumos)
* clínicas com medicamentos de uso interno
* gestão de equipamentos consumíveis

NÃO é módulo crítico no MVP da maioria das clínicas.

Entra na Fase 2 como diferencial.

---

# 2. OBJETIVOS

Garantir:

* controle de entrada/saída
* rastreabilidade por lote/validade
* alertas de baixo estoque
* alertas de validade
* dedução automática em procedimentos
* relatórios de consumo
* compliance sanitária (lotes, validade)

---

# 3. PRINCÍPIOS

* todo item com identificação única
* todo movimento rastreado
* todo lote rastreado
* validade respeitada (FIFO)
* nunca permitir saldo negativo silencioso

---

# 4. CATEGORIAS DE ITEM

* materiais de consumo
* medicamentos
* equipamentos consumíveis
* reagentes laboratoriais
* materiais de escritório (opcional)

---

# 5. ENTIDADES

## Principais

* `StockItem` — item base
* `StockBatch` — lote específico
* `StockMovement` — entrada/saída
* `StockSupplier` — fornecedor
* `StockPurchaseOrder` — pedido de compra
* `StockUsageLink` — vínculo de uso (procedimento, paciente)

---

# 6. CADASTRO DE ITEM

Campos:

* nome
* SKU/código interno
* descrição
* categoria
* unidade (un, ml, g, kg, caixa)
* quantidade mínima
* quantidade ideal
* fornecedor principal
* custo unitário médio
* localização (sala, armário)
* requer prescrição? (sim/não)

---

# 7. LOTES

Todo medicamento e material sanitário rastreado por lote:

* número do lote
* fabricante
* data de fabricação
* data de validade
* quantidade recebida
* quantidade restante
* nota fiscal vinculada
* fornecedor

---

# 8. MOVIMENTOS

## Tipos

* entrada por compra
* entrada por doação
* entrada por transferência (multiunidade)
* saída por uso em procedimento
* saída por descarte
* saída por validade
* saída por transferência
* ajuste de inventário

---

## Cada movimento registra

* item
* lote (quando aplicável)
* quantidade
* tipo
* motivo
* responsável
* paciente vinculado (se uso clínico)
* procedimento vinculado (se aplicável)
* timestamp

---

# 9. ALERTAS

Configuráveis por item:

* atingiu quantidade mínima
* atingiu zero
* lote próximo da validade (30, 60, 90 dias)
* lote vencido
* consumo anômalo (subitamente alto)

---

# 10. FIFO

Sistema sugere uso do lote mais antigo válido:

* ao registrar uso, sugere lote
* operador pode mudar com justificativa
* descartes de validade automatizados

---

# 11. PEDIDOS DE COMPRA

Funcionalidades:

* gerar pedido baseado em estoque mínimo
* selecionar fornecedor
* gerar PDF do pedido
* enviar por email/WhatsApp
* registrar status:
  * `draft`
  * `submitted`
  * `confirmed`
  * `received`
  * `cancelled`
* dar entrada no estoque ao receber

---

# 12. INVENTÁRIO

Periódico:

* contagem manual
* comparação com saldo do sistema
* ajuste com justificativa
* relatório de discrepâncias
* audit completo

---

# 13. DEDUÇÃO AUTOMÁTICA

Quando procedimento é realizado:

* materiais usados deduzidos automaticamente
* configurado por tipo de procedimento
* operador pode adicionar/remover na hora
* exige confirmação visual

Ex: limpeza dental usa 1 par luvas + 1 máscara + 0.5g pasta.

---

# 14. INTEGRAÇÃO COM PROCEDIMENTOS

Cada tipo de procedimento tem:

* materiais padrão consumidos
* quantidade por execução
* opcionais

Atualização da tabela mantém histórico.

---

# 15. INTEGRAÇÃO COM FINANCEIRO

* custo unitário rastreia para análise de margem
* DRE por procedimento considera custo de material
* lançamento de compras vai para `accounts_payable`

---

# 16. INTEGRAÇÃO COM LABORATÓRIO

Específico:

* reagentes vinculados a tipos de exame
* dedução por exame processado
* alerta antes de processar se sem reagente

---

# 17. DASHBOARD

KPIs essenciais:

* itens em alerta de mínimo
* itens em alerta de validade
* valor total em estoque
* giro de estoque
* itens com maior consumo
* descarte por validade (perda)

---

# 18. RELATÓRIOS

Periódicos:

* movimentação mensal
* consumo por profissional
* consumo por procedimento
* perdas por validade
* custo de estoque
* sugestão de compra

---

# 19. CONTROLE SANITÁRIO

Para clínicas em fiscalização da vigilância:

* rastreio completo por lote
* prazos de validade documentados
* descartes documentados
* relatórios exportáveis para fiscalização

---

# 20. MEDICAMENTOS CONTROLADOS

Categoria especial:

* registro adicional rigoroso
* SNGPC (Sistema Nacional de Gerenciamento de Produtos Controlados) — Fase 3
* receituário associado
* armazenamento separado
* contagem mais frequente

---

# 21. MULTI-UNIDADE

Estoque por unidade:

* visualização consolidada (matriz)
* transferência entre unidades
* aprovação de transferência
* histórico de movimentação

---

# 22. PERMISSÕES

| Ação | Quem |
|------|------|
| Cadastrar item | admin, gestor |
| Dar entrada | recepção, financeiro |
| Dar saída clínica | profissional |
| Inventário | gestor |
| Pedidos de compra | financeiro, gestor |
| Relatórios | gestor |
| Itens controlados | usuários autorizados |

---

# 23. AUDITORIA

Toda movimentação:

* quem
* quando
* item
* lote
* quantidade
* motivo
* paciente/procedimento vinculado (quando aplicável)

Auditoria adicional em medicamentos controlados.

---

# 24. PERFORMANCE

* listagens com paginação e filtros
* dashboards com cache invalidado por movimento
* índices em `(tenant_id, item_id, created_at)`

---

# 25. SEGURANÇA

* dados de estoque restritos por RBAC
* nunca expor entre tenants
* logs reforçados em controlados
* relatórios assinados quando exigido por fiscalização

---

# 26. ROADMAP

## Fase 1 (MVP)

* não incluído

---

## Fase 2

* cadastro de itens
* movimentação básica (entrada/saída)
* alerta de baixo estoque
* alerta de validade
* relatórios básicos

---

## Fase 2.5

* dedução automática em procedimentos
* lotes detalhados
* pedidos de compra
* multi-unidade
* integração com laboratório

---

## Fase 3

* medicamentos controlados (SNGPC)
* IA prediz necessidade de compra
* análise de margem por procedimento
* integração com fornecedores via API

---

# 27. O QUE EVITAR

Nunca permitir:

* saldo negativo silencioso
* uso de lote vencido
* movimentação sem motivo
* dedução automática sem confirmação
* descarte sem registro
* item sem unidade definida

---

# 28. OBJETIVO FINAL

Construir um módulo de estoque que:

* dá visibilidade real do consumo
* evita perdas por validade
* alimenta análise de margem
* atende vigilância sanitária
* é simples para clínicas pequenas e robusto para laboratórios
