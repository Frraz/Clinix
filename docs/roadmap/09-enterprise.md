# Fase 6 — Enterprise

> Atender redes de clínicas, franquias, grandes operações. Sob demanda.

---

# 1. OBJETIVO

Enterprise é resposta à demanda real, não construção especulativa. Esta fase entrega:

* **SSO** (SAML 2.0, OIDC)
* **Tenant dedicado** opcional (instância isolada por contrato)
* **SLA enterprise** (99.95% uptime, suporte prioritário, RTO/RPO curtos)
* **Gerente de conta** dedicado
* **Customizações por contrato** (com curadoria)
* **Relatórios consolidados multi-clínica** (rede)
* **Operação de redes/franquias** (governança, padronização, royalties)
* **Auditoria avançada** (LGPD + ISO 27001 readiness)
* **Compliance enterprise** (HIPAA-ready se exportável; ISO; SOC 2 light)

Cada contrato enterprise é um projeto. Padrões existem para acelerar.

---

# 2. PRÉ-REQUISITOS

* Fase 5 (Plataforma) com API estável
* 30+ clínicas pagantes
* MRR > R$ 50.000/mês (sustenta time enterprise)
* primeiro lead enterprise qualificado (rede com 5+ unidades)
* runbook + DR validados em produção

---

# 3. DURAÇÃO ESTIMADA

**Sob demanda**. Não há timeline fixa.

Padrão por contrato enterprise: 3–6 meses de implantação + acompanhamento contínuo.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Backend dev sênior | 1 | 2 |
| Frontend dev | 1 | 1 |
| SRE | 1 | 1 |
| Account manager | 1 | 1 |
| Security engineer (consult) | parcial | 1 |
| Customer success enterprise | 1 | 1 |

---

# 5. ENTREGÁVEIS PRINCIPAIS (sob demanda)

## SSO

* [ ] SAML 2.0 (Microsoft Entra ID, Okta, Google Workspace)
* [ ] OIDC genérico
* [ ] SCIM para provisionamento automático
* [ ] mapeamento de claims para roles do Clinix
* [ ] fallback para login normal (admin de emergência)

## Tenant dedicado

* [ ] instância Docker isolada para o cliente
* [ ] banco dedicado (não shared)
* [ ] storage dedicado (bucket próprio no R2)
* [ ] domínio próprio sem CNAME (custom DNS)
* [ ] backup independente com retenção customizada

## SLA Enterprise

* [ ] 99.95% uptime mensal
* [ ] RTO < 1h
* [ ] RPO < 15 min (PITR via WAL)
* [ ] suporte prioritário (chat 24/7 ou SLA específico)
* [ ] hotline para incidentes
* [ ] créditos por descumprimento

## Operação de rede

* [ ] hierarquia: rede > clínicas > unidades
* [ ] gestão centralizada (rede vê todas as clínicas)
* [ ] relatórios consolidados multi-clínica
* [ ] padronização forçada (templates da rede)
* [ ] royalties por unidade (relatório financeiro)
* [ ] segregação opcional de dados sensíveis por clínica

## Customizações por contrato

* [ ] processo formal de avaliação de pedido (effort + ROI)
* [ ] desenvolvimento isolado em branch dedicado
* [ ] revisão arquitetural antes de aceitar
* [ ] curadoria: o que vira feature core vs custom permanece

## Compliance enterprise

* [ ] ISO 27001 readiness check
* [ ] SOC 2 light report anual
* [ ] HIPAA-ready se exportável (não BR-only)
* [ ] auditoria externa anual
* [ ] DPO formalmente designado

---

# 6. STACK ENTERPRISE

| Tech | Para que |
|------|----------|
| django-saml2-auth ou djangosaml2 | SAML SP |
| Mozilla django-oidc-provider | OIDC IdP/SP |
| SCIM 2.0 spec | provisionamento |
| WAL-G ou pgBackRest | PITR |
| HashiCorp Vault | secrets enterprise |
| OPA (Open Policy Agent) | policy as code |
| Datadog / New Relic | observability enterprise |
| PagerDuty | on-call |

---

# 7. KPIs DE SAÍDA (por contrato)

* [ ] contrato enterprise assinado > R$ 5.000/mês
* [ ] implantação em < 90 dias
* [ ] SLA atingido no primeiro trimestre
* [ ] NPS enterprise > 8
* [ ] zero incidente de segurança no primeiro ano

---

# 8. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Customização vira tech debt | alta | crítico | curadoria forte; PRD obrigatório; aceitar dizer não |
| Tenant dedicado infla custo de ops | alta | médio | premium pricing; automação de provisionamento |
| SSO complexo por IdP | alta | médio | suportar 3–4 IdPs principais primeiro |
| ISO/SOC custosos | média | alto | start com gap analysis; perseguir só com cliente prometendo contrato |
| Suporte 24/7 esgota time | alta | alto | suporte tier-based; ferramentas de self-service |
| Cliente enterprise puxa roadmap | alta | médio | governança de produto; pequenas customizações sim, mudanças de produto não |

---

# 9. ANTI-METAS

NÃO fazer na Fase 6:

* aceitar todo pedido enterprise (custos podem matar margem)
* customizar visualmente além de branding (manutenção)
* prometer SLA sem capacidade real
* operar tenant dedicado sem premium pricing
* manter código forkado por cliente (caminho do desastre)
* expandir para mercado internacional sem plano específico

---

# 10. CUSTOS DA FASE (por contrato)

| Item | Custo |
|------|-------|
| Implantação inicial | R$ 30.000–80.000 (cobrado do cliente) |
| Tenant dedicado infra | R$ 1.000–3.000/mês |
| Account manager | R$ 8.000–15.000/mês (parcial) |
| SRE (proporcional) | R$ 5.000–10.000/mês |
| Compliance/auditoria | R$ 20.000–50.000/ano (anual) |
| **Total custo operacional** | varia por contrato |

Receita esperada por contrato: > 5× o custo operacional.

---

# 11. PROCESSO DE CONTRATO ENTERPRISE

```txt
Lead qualificado
   ↓
Discovery call (1h)
   ↓
Análise de viabilidade técnica (1 sem)
   ↓
Proposta comercial + escopo
   ↓
Negociação + assinatura
   ↓
Kickoff
   ↓
Implantação (3-6 meses):
   - setup técnico
   - migração de dados
   - customizações aprovadas
   - treinamento
   - go-live faseado
   ↓
Hipercare (30 dias)
   ↓
Operação + acompanhamento mensal
   ↓
Renovação anual
```

---

# 12. CRITÉRIO DE SUCESSO

Por contrato:

* [ ] go-live no prazo
* [ ] SLA cumprido
* [ ] NPS > 8
* [ ] renovação após 12 meses

Para a fase como um todo:

* [ ] 3+ contratos enterprise ativos
* [ ] enterprise representa 30%+ do MRR
* [ ] processo de implantação padronizado em < 90 dias

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — vendas enterprise consultiva; eventos médicos de grandes redes; relacionamento direto com C-level
* **Cadência operacional** — postmortems mandatórios; on-call dedicado enterprise; security review semestral

---

# 14. EXEMPLO REAL HIPOTÉTICO

Cenário: **Rede de clínicas regionais com 8 unidades em 4 cidades** procura o Clinix.

* Discovery: identificar pontos de dor (gestão consolidada, padronização)
* Análise: stack atual permite multi-unidade já em Fase 2 — basta hierarquia rede > clínica > unidade
* Proposta: R$ 8.000/mês + R$ 50.000 setup
* Implantação: 90 dias com 1 backend + 1 frontend + 1 CS
* Go-live: cidade por cidade (15 dias cada)
* Hipercare: 30 dias com CS no local
* Operação: account manager mensal; relatório consolidado

---

# 15. REFERÊNCIAS

* `docs/auth.md` (SSO)
* `docs/tenancy.md` (tenant dedicado)
* `docs/backup-dr.md` (RTO/RPO)
* `docs/compliance-conselhos.md`
* `docs/lgpd.md`
* `docs/auditoria.md`
* `docs/suporte.md`
* `docs/roadmap/08-plataforma.md` (fase anterior)
* `docs/roadmap/10-go-to-market.md` (paralelo)
* `docs/roadmap/11-cadencia-operacional.md` (paralelo)
