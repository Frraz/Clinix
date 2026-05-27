# Sprint 05 — Bugs do Piloto + Performance + UX Refinements

> Primeira sprint da Fase 1.5 (Polimento). Foco em estabilizar o piloto e aplicar feedback real.

---

# 1. OBJETIVO

Após 30 dias com clínica piloto em produção, esta sprint:

* corrige bugs descobertos em operação real
* aplica refinamentos de UX baseados em feedback estruturado
* otimiza performance dos endpoints e queries mais usados
* fecha pontas soltas do MVP

Ao final, o produto está **maduro para vender para a segunda e terceira clínica** sem retrabalho operacional.

---

# 2. DURAÇÃO ESTIMADA

3 semanas.

---

# 3. PRÉ-REQUISITOS

Fase 1 concluída:

* clínica piloto em produção há 30+ dias
* feedback estruturado coletado
* lista priorizada de bugs e melhorias

---

# 4. ESCOPO

## Bugs do piloto

* [ ] resolver todos os bugs P0 (críticos / impedem operação) reportados
* [ ] resolver bugs P1 (degradação importante) reportados
* [ ] documentar bugs P2 e P3 no backlog para sprints futuras
* [ ] adicionar testes de regressão para cada bug crítico corrigido

---

## Performance

* [ ] revisar top 10 queries mais lentas em produção (`pg_stat_statements`)
* [ ] adicionar índices GIN onde Postgres recomendar
* [ ] N+1 queries identificadas e corrigidas (django-debug-toolbar em staging)
* [ ] paginação otimizada (cursor-based onde fizer sentido)
* [ ] cache de templates de prontuário (Redis)
* [ ] cache de listagens curtas (5 min TTL)
* [ ] lazy load das imagens em todas as timelines
* [ ] code splitting por rota no Next.js
* [ ] preload de rotas críticas
* [ ] otimização de bundle (`@next/bundle-analyzer`)

---

## UX refinements

* [ ] empty states refinados em todas as listagens
* [ ] error states refinados (mensagens claras + retry)
* [ ] skeleton loading nas tabelas e cards
* [ ] tooltips em ações não-óbvias
* [ ] atalhos de teclado nas telas críticas (agenda, prontuário)
* [ ] busca global (Cmd+K) inicial
* [ ] toasts consistentes para feedback de ações
* [ ] confirmação antes de ações destrutivas
* [ ] estados de loading bloqueando dupla submissão
* [ ] navegação melhorada em mobile (hambúrguer + bottom nav onde fizer sentido)

---

## Hardening operacional

* [ ] dashboard de saúde do sistema (Sentry + Uptime)
* [ ] runbook de incidentes documentado
* [ ] alertas críticos configurados (Slack/Discord webhook)
* [ ] rate limiting refinado para endpoints sensíveis
* [ ] logs JSON com `request_id` em 100% dos endpoints

---

## Pequenas funcionalidades pedidas

* [ ] export operacional em Excel (xlsx)
* [ ] export de agenda em iCal
* [ ] templates de email com branding aplicado
* [ ] preview ao vivo de personalização (logo, cor)
* [ ] dark mode opt-in

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* zero bugs P0 abertos
* < 5 bugs P1 abertos
* top 10 queries < 200ms p95
* primeira tela < 1.5s p95
* feedback positivo do piloto em entrevista de validação
* documentação de runbook completa
* clínica piloto declara "está mais rápido"

---

# 6. NÃO ESTÁ NO ESCOPO

* aprovação de templates WhatsApp na Meta (Sprint 06)
* portal do paciente v2 (Sprint 06)
* CRM (Sprint 07)
* multi-unidade (Sprint 07)
* convênios/TISS (Sprint 08)

---

# 7. RISCOS

* feedback do piloto inflando escopo
  * mitigação: timeboxing + qualquer pedido novo vai pro backlog, não pra sprint
* otimização prematura
  * mitigação: medir antes de otimizar; só atacar top 10 medido
* refatorações grandes mascaradas como "polish"
  * mitigação: code review rigoroso; refator significativo gera ADR

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando
* code review aprovado
* validação em staging com dados reais (anonimizados)
* sem regressão nas métricas de performance

---

# 9. PRÓXIMA SPRINT

Sprint 06 cobrirá:

* hardening LGPD completo
* templates WhatsApp aprovados na Meta
* portal do paciente v2 com mais recursos
* primeiro NPS estruturado

Ver `tasks/sprint-06.md`.
