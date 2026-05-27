# Performance — Clinix

> Metas formais de performance, SLOs e práticas de otimização do Clinix.

---

# 1. VISÃO GERAL

Performance no Clinix é diferencial competitivo.

Sistemas concorrentes regionais são lentos.

Velocidade percebida é uma das primeiras coisas que vendem o produto.

Este doc define:

* metas mensuráveis
* SLOs por categoria
* error budgets
* práticas obrigatórias
* monitoramento

---

# 2. OBJETIVOS

Garantir:

* sistema rápido em telas críticas
* operação fluida em uso contínuo
* escala sem degradação proporcional
* visibilidade clara de gargalos
* prevenção antes de incidente

---

# 3. PRINCÍPIOS

* metas formalizadas, não opiniões
* medir antes de otimizar
* otimizar onde dói (não onde é "elegante")
* performance é responsabilidade do dev, não do "alguém depois"
* regressão de performance é bug

---

# 4. SLOs PRINCIPAIS

## API REST

| Métrica | Meta | Bloqueio |
|---------|------|----------|
| Latência p50 | < 100ms | > 200ms |
| Latência p95 | < 500ms | > 1s |
| Latência p99 | < 1s | > 2s |
| Disponibilidade | 99.5% MVP / 99.9% Enterprise | < 99% |
| Error rate | < 0.5% | > 1% |

---

## Frontend Web

| Métrica | Meta | Bloqueio |
|---------|------|----------|
| First Contentful Paint (FCP) | < 1.5s | > 3s |
| Largest Contentful Paint (LCP) | < 2.5s | > 4s |
| Time to Interactive (TTI) | < 3s | > 5s |
| Cumulative Layout Shift (CLS) | < 0.1 | > 0.25 |
| First Input Delay (FID) | < 100ms | > 300ms |

---

## Portal do Paciente (Mobile-First)

Mais rígido (3G considerado):

| Métrica | Meta |
|---------|------|
| FCP em 3G | < 2s |
| LCP em 3G | < 3.5s |
| Tamanho de bundle inicial | < 200KB gzipped |

---

## Tarefas Assíncronas

| Tarefa | Meta |
|--------|------|
| Envio de WhatsApp | < 10s |
| Geração de PDF (receita) | < 5s |
| Geração de PDF (relatório) | < 30s |
| Export de CSV (< 10k registros) | < 30s |
| Export completo de tenant | < 24h |

---

# 5. ERROR BUDGETS

Tolerância mensal:

* 99.5% uptime = 3h 39min de downtime/mês permitido
* 99.9% uptime = 43min/mês
* error rate 0.5% = 1 erro a cada 200 requests

Quando budget esgota:

* feature freeze
* foco em estabilidade
* post-mortem
* ações de mitigação

---

# 6. ENDPOINTS CRÍTICOS

SLOs mais rígidos:

* `POST /api/v1/auth/login` — < 200ms p95
* `POST /api/v1/appointments` — < 300ms p95
* `GET /api/v1/agenda/today` — < 200ms p95
* `GET /api/v1/patients/:id` — < 150ms p95
* `POST /api/v1/whatsapp/send` — < 100ms p95 (enfileira e retorna)

---

# 7. PRÁTICAS OBRIGATÓRIAS — BACKEND

## Database

* índice em toda FK
* índice em `(tenant_id, created_at)` nas tabelas grandes
* `select_related` para FK acessadas
* `prefetch_related` para reverse FK / M2M
* `only()` / `defer()` em queries pesadas
* `EXPLAIN` em queries suspeitas
* monitoramento de queries lentas (>200ms)
* particionamento em tabelas que crescem muito (audit_logs, whatsapp_messages, notifications)

---

## Cache

* cache de respostas idempotentes
* TTLs apropriados
* invalidação por evento
* cache por tenant
* nunca cache cross-tenant

---

## Assíncrono

* nada que demore > 500ms na request principal
* Celery para qualquer I/O lento
* WhatsApp sempre via fila
* envio de email sempre via fila
* geração de PDF de relatório sempre via fila

---

## API

* paginação obrigatória em listagens
* limites em filtros (`page_size` máx 100)
* rate limiting
* compressão gzip
* ETag em recursos cacheáveis

---

# 8. PRÁTICAS OBRIGATÓRIAS — FRONTEND

## Bundle

* code splitting por rota (Next.js App Router)
* lazy load de componentes pesados (FullCalendar, TipTap)
* dynamic imports para módulos opcionais
* análise de bundle mensal (não passar de 200KB inicial)

---

## Renderização

* React Server Components onde aplicável
* memoização (`useMemo`, `useCallback`) quando justificado
* virtualization em listas longas
* skeleton loading em vez de spinners
* nunca re-render desnecessário

---

## Dados

* TanStack Query com cache configurado
* invalidação inteligente (não nuclear)
* prefetch em hover quando previsível
* optimistic updates em ações comuns

---

## Imagens

* `next/image` com lazy loading
* tamanhos apropriados (sem servir 4K para thumbnail)
* WebP/AVIF
* CDN via Cloudflare

---

## Fontes

* `next/font` com display swap
* preload das fontes críticas
* fallback nativo

---

# 9. PRÁTICAS OBRIGATÓRIAS — INFRA

## Nginx

* gzip ativo
* brotli ativo
* cache de static assets (1 ano)
* HTTP/2 + HTTP/3
* keep-alive

---

## Postgres

* `pgbouncer` quando passar de 100 conexões
* `pg_stat_statements` ativo
* autovacuum tunado
* tablespaces apropriados
* monitoramento de bloat

---

## Redis

* `maxmemory-policy: allkeys-lru` para cache puro
* monitoramento de hit rate
* alerta se memória > 80%

---

## Cloudflare

* CDN para todos os assets estáticos
* page rules para cache agressivo
* DDoS protection
* WAF básico

---

# 10. MONITORAMENTO

## Stack

* Sentry → erros
* Sentry Performance → traces
* Prometheus → métricas de infraestrutura (Fase 2)
* Grafana → dashboards
* Cloudflare Analytics → tráfego web

---

## Dashboards essenciais

* SLOs em tempo real
* error rate por endpoint
* latência p50/p95/p99 por endpoint
* fila Celery (tamanho, latência)
* uso de DB (conexões, queries lentas)
* uso de Redis (hit rate, memória)
* Core Web Vitals (frontend)

---

# 11. ALERTAS

Alertas automáticos:

* error rate > 1% por 5min
* latência p95 > 1s por 5min
* fila Celery > 1000 itens
* DB connections > 80%
* memória do Redis > 80%
* uso de disco > 85%
* uptime check falhou

---

# 12. PROFILING

## Backend

* django-debug-toolbar em dev
* django-silk para análise
* py-spy em prod para casos extremos
* Sentry Performance traces

---

## Frontend

* React DevTools Profiler
* Lighthouse no CI
* Web Vitals em prod
* Chrome DevTools Performance

---

# 13. LOAD TESTING

Antes de cada major release:

* k6 ou Locust
* cenários realistas (login, criar consulta, listar agenda)
* meta: 100 reqs/s por instância sem degradação
* identificar gargalos antes do cliente

---

# 14. REGRESSÕES

Toda PR que pode impactar performance:

* benchmark de endpoint afetado
* análise de queries (django-silk)
* comparativo before/after
* code review específico

CI bloqueia PRs com regressão significativa.

---

# 15. PERFORMANCE BUDGETS POR PÁGINA

| Tela | LCP máximo | Bundle máximo |
|------|------------|---------------|
| Login | 1.5s | 100KB |
| Dashboard | 2s | 250KB |
| Agenda | 2.5s | 350KB (FullCalendar) |
| Prontuário | 2.5s | 400KB (TipTap) |
| Portal do paciente | 1.5s | 200KB |

---

# 16. CAPACITY PLANNING

Estimativa MVP:

* 1 VPS suporta ~50 clínicas ativas
* limite real medido em load test
* alertas em 70% da capacidade
* upgrade planejado antes de saturar

Fase 2+:

* horizontal scaling com load balancer
* Postgres em instância dedicada
* Redis em instância dedicada
* CDN para assets

---

# 17. DEGRADAÇÃO GRACIOSA

Sistema deve degradar com elegância:

* IA indisponível → feature desabilitada com aviso
* WhatsApp falhando → fallback para email
* PDF lento → fila com notificação
* relatório grande → assíncrono com progresso
* nunca travar usuário em loading infinito

---

# 18. PERFORMANCE COMO FEATURE

Comunicar ao cliente:

* "Carregamento de agenda < 1s"
* "PDF de receita instantâneo"
* "Funciona em internet ruim do interior"

Velocidade é argumento de venda.

---

# 19. ANTI-PATTERNS

Nunca fazer:

* N+1 queries
* loops com query dentro
* sync HTTP call em request handler
* listagem sem paginação
* JOIN em tabelas grandes sem índice
* serialização de objetos inteiros quando só precisa de 2 campos
* bundle gigante por importar lib inteira
* renderização sem chave em listas
* `useEffect` com dependência ausente

---

# 20. AUDITORIA DE PERFORMANCE

Periódica:

* mensal: top 20 queries lentas
* mensal: top 10 endpoints lentos
* trimestral: análise de bundle frontend
* trimestral: revisão de cache hit rates
* anual: load test completo

Cada auditoria gera ações concretas.

---

# 21. PERFORMANCE EM TENANT GRANDE

Cliente Enterprise com 100k+ pacientes:

* particionamento de tabelas
* índices específicos
* dashboards com agregações materializadas
* cache mais agressivo
* possivelmente read replica
* potencialmente instância dedicada

---

# 22. PERFORMANCE EM MOBILE

* otimização agressiva de bundle
* image optimization
* offline cache (PWA — Fase 2)
* sync inteligente
* push em vez de poll

Ver `docs/mobile.md`.

---

# 23. ROADMAP

## Fase 1 (MVP)

* Sentry Performance configurado
* monitoramento básico (Cloudflare + logs)
* SLOs documentados
* dashboard básico
* alertas críticos

---

## Fase 1.5

* dashboards completos no Grafana
* alertas refinados
* análise mensal de queries lentas
* otimizações baseadas em produção

---

## Fase 2

* Prometheus
* load testing automatizado no CI
* performance budgets enforced no CI
* particionamento de tabelas grandes
* pgbouncer

---

## Fase 3+

* read replicas
* sharding (se necessário)
* CDN agressiva para assets clínicos
* instâncias dedicadas para Enterprise

---

# 24. RESPONSABILIDADES

## Time de produto

* metas alinhadas com SLOs
* não exigir feature sem capacidade
* aceitar trade-offs

---

## Time de engenharia

* implementar com performance em mente
* medir antes de otimizar
* alertar sobre regressões
* respeitar performance budgets

---

## Time de infra

* monitorar saúde
* capacity planning
* responder a incidentes
* manter ferramentas atualizadas

---

# 25. OTIMIZAÇÃO DE IMAGENS CLÍNICAS

Imagens clínicas (antes/depois em estética, dermatoscopia, fotos odontológicas) representam alto volume e exigem estratégia dedicada.

## Pipeline padrão

1. upload via **presigned PUT** direto ao R2 (zero carga no backend)
2. compressão em **Celery** (`pillow`, `pillow-heif`, `pillow-avif`)
3. geração de thumbnails: 200 / 800 / 2000 px
4. saída em **WebP** (com fallback AVIF onde suportado)
5. CDN da Cloudflare com `Cache-Control: public, immutable` (URL contém hash)
6. original servido via URL assinada (TTL curto, 5–15 min)

## Limites

* upload máximo: 25 MB
* dimensão máxima: 8000 × 8000 px
* formatos aceitos: JPEG, PNG, HEIC, WebP
* validação por magic bytes (não confiar no mime do cliente)

## Lazy loading

* `<img loading="lazy">` em timelines
* IntersectionObserver para timelines longas
* thumbs 200px para listagens, 800px inline, 2000px ampliação

## Antes/depois

* slider client-side em SVG/canvas (sem requisição extra)
* preload da contra-parte pareada
* aviso visual quando metadados (ângulo, iluminação) divergem

## Quotas

* armazenamento por tenant monitorado
* plano com limite de storage (ver `docs/billing.md`)
* anonimização de fotos antigas conforme política

Detalhes em `docs/imagens-clinicas.md` e `ADRs/007-storage-cloudflare-r2.md`.

---

# 26. OBJETIVO FINAL

Construir um sistema que:

* o cliente comenta na primeira semana ("nossa, é rápido")
* nunca trava em tela cheia
* funciona bem no interior com internet ruim
* escala sem reescrita
* tem métricas claras de saúde
* nunca surpreende em incidente
