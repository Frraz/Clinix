# Suporte ao Cliente — Clinix

> Estratégia, canais, SLAs e processo de suporte do Clinix.

---

# 1. VISÃO GERAL

Suporte é diferencial competitivo no nicho regional.

Sistemas grandes têm suporte ruim ou genérico.

O Clinix vai vencer regionalmente em parte pela proximidade.

Este doc define:

* canais
* SLAs por plano
* tipos de solicitação
* processo de escalação
* base de conhecimento
* time

---

# 2. OBJETIVOS

Garantir:

* clínica nunca sente abandonada
* problema resolvido rápido
* canal apropriado para cada tipo
* aprendizado do produto via tickets
* prevenção de churn por frustração

---

# 3. PRINCÍPIOS

* humano quando importa
* self-service quando preferível
* respostas rápidas mesmo sem solução imediata
* nunca esconder limitação do produto
* feedback do suporte vira backlog do produto

---

# 4. CANAIS

| Canal | Uso | Plano |
|-------|-----|-------|
| Base de conhecimento | self-service | todos |
| In-app | dúvida rápida + bug | todos |
| Email | solicitação formal | todos |
| WhatsApp | urgente | Professional+ |
| Telefone | crítico | Business+ |
| Reunião agendada | onboarding, treinamento | conforme plano |
| Gerente de conta | conta dedicada | Enterprise |

---

# 5. SLA POR PLANO

## Tempo de primeira resposta

| Severidade | Starter | Professional | Business | Enterprise |
|------------|---------|--------------|----------|------------|
| Crítica (sistema fora) | 4h | 1h | 30min | 15min |
| Alta (módulo crítico) | 8h | 4h | 2h | 1h |
| Média (parcial) | 24h | 8h | 4h | 2h |
| Baixa (dúvida) | 48h | 24h | 12h | 4h |

---

## Tempo de resolução (alvo)

| Severidade | Alvo geral |
|------------|------------|
| Crítica | 4h |
| Alta | 1 dia útil |
| Média | 3 dias úteis |
| Baixa | 7 dias úteis ou ciclo de produto |

---

## Horário de atendimento

* Starter/Professional: comercial (8h-18h, seg-sex)
* Business: estendido (7h-22h, todos os dias)
* Enterprise: 24/7 para incidentes críticos

---

# 6. CLASSIFICAÇÃO DE SEVERIDADE

## Crítica

* sistema fora completo
* perda de dados
* vazamento de dados
* impossibilidade de operar a clínica

---

## Alta

* módulo crítico fora (agenda, prontuário, auth)
* falha em integração crítica (WhatsApp, pagamento)
* bug bloqueador

---

## Média

* módulo secundário fora
* funcionalidade com workaround
* erro recorrente sem bloquear

---

## Baixa

* dúvida de uso
* sugestão
* erro cosmético

---

# 7. TIPOS DE SOLICITAÇÃO

## Dúvida

Como usar o sistema, fluxo operacional, configuração.

→ Resposta direta + link da base de conhecimento.

---

## Bug

Algo não funciona como esperado.

→ Triagem técnica → registro em `tasks/bugs.md` → resolução pelo dev.

---

## Sugestão de feature

Cliente quer algo que não existe.

→ Registro em backlog → análise pelo produto → resposta com decisão.

---

## Incidente

Sistema com problema afetando vários clientes.

→ Resposta padronizada → status page → comunicação proativa.

---

## Treinamento

Cliente precisa de capacitação adicional.

→ Agendamento de sessão → material reforço.

---

## Personalização

Cliente quer customizar algo do sistema.

→ Validação se está no plano → execução ou orçamento.

---

## Importação

Cliente quer migrar dados.

→ Time de Sucesso conduz.

Ver `docs/importacao-exportacao.md`.

---

# 8. PROCESSO

```txt
Ticket aberto
   ↓
Triagem (severidade + tipo)
   ↓
Atribuição
   ↓
Primeira resposta dentro do SLA
   ↓
Investigação
   ↓
Resolução OU escalação
   ↓
Validação com o cliente
   ↓
Fechamento
   ↓
Pesquisa de satisfação (opcional)
```

---

# 9. ESCALAÇÃO

## Níveis

* **N1 — Atendimento** — suporte geral, dúvidas, problemas simples
* **N2 — Especialista** — bugs, configurações avançadas, integrações
* **N3 — Engenharia** — bugs profundos, performance, infra

Escalação por tempo ou complexidade.

Cliente nunca espera por escalação burocrática.

---

## Escalação interna

* N1 → N2: ticket transferido, contexto preservado
* N2 → N3: cria task técnica, mantém ticket aberto, atualiza cliente
* N3 → equipe core: incidente, post-mortem obrigatório

---

# 10. BASE DE CONHECIMENTO

Pública (clientes e prospects):

* artigos organizados por módulo
* vídeos curtos (< 3min)
* GIFs animados em fluxos
* FAQ por perfil
* solução para problemas comuns

---

## Métricas

* artigos mais lidos
* artigos com baixa avaliação (revisar)
* tickets evitados (baseado em busca prévia)

---

## Sugestão de melhoria

Cliente pode:

* avaliar artigo
* sugerir mudança
* sugerir novo artigo

Suporte cria/atualiza com base no uso real.

---

# 11. AJUDA CONTEXTUAL IN-APP

Em cada tela:

* botão de ajuda (?)
* tooltip explicativo
* link para artigo da base
* chat de suporte direto (Fase 2)
* tour guiado nas telas complexas

---

# 12. TICKETS

## Sistema

* MVP: email + planilha de controle interna
* Fase 1.5: ferramenta dedicada (ex: Plain, Pylon, Hubspot Service)
* Fase 2: integração com Sentry (bug técnico → ticket)

---

## Cada ticket carrega

* tenant
* usuário que abriu
* canal
* severidade
* tipo
* descrição
* anexos (screenshots, vídeos)
* timeline de interações
* responsável atual
* tags
* tempo de resposta
* tempo de resolução

---

# 13. AUTOMAÇÕES

## Resposta automática

* confirmação de abertura
* SLA esperado
* link para acompanhar

---

## Lembrete

* cliente sem responder ticket aberto há 3 dias → lembrete
* ticket sem ação interna há 1 dia → alerta para suporte

---

## Fechamento

* sem resposta do cliente há 7 dias → fecha com aviso
* opção de reabertura por 30 dias

---

# 14. SATISFAÇÃO

CSAT pós-ticket:

* "Sua dúvida foi resolvida?" (sim/não)
* "De 1 a 5, como foi o atendimento?"
* campo livre opcional

Dashboard:

* CSAT médio
* CSAT por agente
* CSAT por tipo de ticket

---

# 15. INDICADORES

KPIs essenciais:

* tickets/mês
* tempo médio de primeira resposta
* tempo médio de resolução
* taxa de cumprimento de SLA
* CSAT
* tickets por cliente (cliente outlier = problema)
* tickets por módulo (módulo outlier = problema)
* tickets que viraram bug
* tickets que viraram feature

---

# 16. TIME

## Estrutura inicial (MVP)

* 1 pessoa: tudo (fundador no início)

---

## Crescimento

* +5 clientes: 1 pessoa suporte parcial
* +20 clientes: 1 pessoa suporte full-time
* +50 clientes: 2 pessoas + 1 Sucesso
* +100 clientes: N1/N2/N3 separados

---

# 17. TREINAMENTO INTERNO

Cada novo membro de suporte:

* treinamento no produto (1 semana)
* shadowing de tickets (1 semana)
* tickets sob supervisão (2 semanas)
* certificação interna
* atualizações mensais sobre novas features

---

# 18. LOOPS COM PRODUTO

Suporte e produto trabalham juntos:

* reunião semanal: top tickets da semana
* identificação de padrões
* priorização de bugs/features
* feedback estruturado

---

# 19. COMUNICAÇÃO PROATIVA

Quando aplicável:

* incidente em curso → comunicação imediata
* manutenção programada → aviso com 7 dias
* nova feature relevante → email
* mudança que impacta operação → aviso com 30 dias
* ajuste de preço → aviso com 60 dias

---

# 20. ESCALAÇÃO PARA O CLIENTE

Quando problema atinge muitos clientes:

* atualização no status page
* email para admins
* WhatsApp para incidentes críticos
* banner no painel
* atualizações frequentes

---

# 21. SUPORTE EM ONBOARDING

Acompanhamento dedicado:

* primeiros 30 dias com contato proativo
* identificação de bloqueios cedo
* prevenção de churn

Ver `docs/onboarding.md`.

---

# 22. PERFIL DE CLIENTE CRÍTICO

Identificação de clientes de risco:

* muitos tickets em pouco tempo
* CSAT consistentemente baixo
* uso decrescente
* feedback negativo
* atraso de pagamento

Time de Sucesso aciona retenção.

---

# 23. POLÍTICA DE COMUNICAÇÃO

## Tom

* humano
* respeitoso
* claro
* sem jargão técnico para o cliente final
* honesto sobre limitações

---

## Nunca

* culpar o cliente
* ignorar
* responder padrão sem ler
* prometer prazo irreal
* esconder bug

---

# 24. ROADMAP

## Fase 1 (MVP)

* email + WhatsApp como canais
* base de conhecimento básica
* tickets em planilha
* SLAs documentados
* atendimento por fundador

---

## Fase 1.5

* ferramenta de tickets dedicada
* base de conhecimento expandida
* ajuda contextual in-app
* CSAT automatizado

---

## Fase 2

* chat de suporte in-app
* primeira pessoa dedicada
* tour guiado nas telas
* segmentação de SLA por plano
* automações de ticket

---

## Fase 3

* IA responde dúvidas simples (chatbot na base)
* sugestão automática de artigo
* análise preditiva de churn por padrão de ticket

---

## Fase 6

* gerente de conta para Enterprise
* SLA contratual
* suporte 24/7

---

# 25. O QUE EVITAR

Nunca permitir:

* ticket sem resposta
* esconder problema do cliente
* SLA não cumprido sem comunicação
* atendimento sem registro
* feedback do cliente que não chega no produto
* canal preferido do cliente ignorado

---

# 26. OBJETIVO FINAL

Construir um suporte que:

* cliente sente próximo
* resolve rápido
* nunca esconde problema
* gera melhoria contínua do produto
* é diferencial competitivo no nicho regional
* evita churn por frustração
