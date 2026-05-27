# Fase 4 — Mobile

> Apps nativos (iOS + Android) para paciente, profissional e gestor.

---

# 1. OBJETIVO

Estender o Clinix para mobile com:

* **App Paciente** — agendamento, check-in, exames, plano, chat, pagamentos
* **App Profissional** — agenda, prontuário, telemedicina, notificações
* **App Gestor** — KPIs, financeiro, indicadores (Fase 4.5 opcional)
* push notifications (FCM)
* biometria (TouchID/FaceID)
* offline básico com sync
* OTA updates (Expo EAS)

Stack: **React Native + Expo SDK 52+**. Reutiliza lógica do web (API + types).

---

# 2. PRÉ-REQUISITOS

* Fase 3 (IA) ou Fase 2 concluída
* 15+ clínicas pagantes ativas
* portal do paciente PWA validado (já tinha tração no web)
* TVL/demanda real medida (>40% dos pacientes pedem app)
* API estável (sem breaking changes em 6+ meses)

---

# 3. DURAÇÃO ESTIMADA

**3–4 meses** (Sprints 20–23, ~3 semanas cada).

App Paciente publica primeiro (mais demanda); App Profissional depois.

---

# 4. TIME RECOMENDADO

| Papel | Mínimo | Confortável |
|-------|--------|-------------|
| Dev mobile (RN) | 1 | 1–2 |
| Backend dev | 1 ajudando | 1 |
| Designer mobile | parcial | 1 |
| QA mobile | parcial | 1 |

---

# 5. SPRINTS DA FASE

## 5.1 Sprint 20 — Setup + Auth + Estrutura Compartilhada (3 sem)

* monorepo Turborepo (ou Nx) com workspace mobile
* Expo SDK 52+ + Expo Router
* TypeScript estrito
* compartilhamento de tipos com web (`packages/types`)
* compartilhamento de client API (`packages/api-client`)
* tema do design system portado para React Native
* Auth JWT + refresh
* OTP via WhatsApp (mesmo do portal)
* MMKV para tokens
* Navegação base (Bottom Tab + Stack)
* CI/CD com EAS Build

## 5.2 Sprint 21 — App Paciente (Pacote 1) (3 sem)

* tela inicial (minhas consultas, próximos compromissos)
* agendamento self-service
* check-in digital
* visualização de receitas/atestados/laudos
* pagamentos PIX (deep link MP)
* push notifications (FCM)
* biometria para abrir app

## 5.3 Sprint 22 — App Paciente (Pacote 2) + Publicação (3 sem)

* visualização de plano alimentar (nutrição)
* visualização de pacotes em andamento (estética/odonto/fisio)
* visualização de fotos clínicas autorizadas
* preenchimento de pré-anamnese
* preenchimento de escalas (psicologia)
* registro de peso semanal (nutrição)
* chat com clínica (omnichannel)
* polimento + publicação na App Store e Play Store

## 5.4 Sprint 23 — App Profissional + Telemedicina (3 sem)

* App Profissional: agenda + check-in + prontuário (versão mobile)
* foto rápida via câmera nativa para anexo
* gravação de áudio para transcrição (preparado para Fase 3 IA)
* envio de orientação ao paciente via WhatsApp do app
* telemedicina via WebRTC (vídeo + chat + compartilhamento de tela)
* sala de espera virtual
* gravação de teleconsulta (opcional, com consentimento)

---

# 6. TABELA DE ENTREGÁVEIS

| Entregável | Sprint | Risco | Prioridade |
|-----------|--------|-------|------------|
| Setup Expo + monorepo | 20 | médio | P0 |
| Compartilhamento de types/client | 20 | médio | P0 |
| Auth JWT + OTP WhatsApp | 20 | médio | P0 |
| Tema design system RN | 20 | médio | P0 |
| EAS Build CI/CD | 20 | médio | P0 |
| App Paciente: agenda + consultas | 21 | médio | P0 |
| App Paciente: documentos | 21 | médio | P0 |
| Pagamentos via deep link | 21 | médio | P0 |
| Push (FCM) | 21 | alto | P0 |
| Biometria | 21 | médio | P0 |
| App Paciente: plano + pacotes | 22 | médio | P0 |
| Pré-anamnese mobile | 22 | médio | P0 |
| Escalas mobile | 22 | médio | P1 |
| Chat omnichannel | 22 | médio | P1 |
| Publicação App Store + Play Store | 22 | alto | P0 |
| App Profissional: core | 23 | alto | P0 |
| Foto/áudio na consulta | 23 | médio | P1 |
| Telemedicina WebRTC | 23 | alto | P0 |
| Sala de espera virtual | 23 | médio | P1 |
| Gravação opcional | 23 | médio | P2 |

---

# 7. STACK NOVA QUE ENTRA NESTA FASE

| Tech | Para que |
|------|----------|
| React Native 0.74+ | framework mobile |
| Expo SDK 52+ | tooling |
| Expo Router | navegação |
| EAS Build + Submit | CI/CD + lojas |
| MMKV | storage local |
| Expo Notifications | push |
| Expo SecureStore | secrets do device |
| Expo Camera | foto/vídeo |
| Expo AV | áudio |
| Expo Biometrics | TouchID/FaceID |
| Daily.co ou Twilio Video ou WebRTC nativo | telemedicina |
| Turborepo ou Nx | monorepo |
| Sentry React Native | erros mobile |

---

# 8. KPIs DE SAÍDA

* [ ] apps publicados em App Store e Play Store
* [ ] rating > 4.3 em ambas as lojas
* [ ] adoção > 40% dos pacientes ativos
* [ ] adoção > 60% dos profissionais
* [ ] uso de telemedicina por 20%+ das clínicas que ativaram
* [ ] crash-free rate > 99.5%
* [ ] tempo médio para primeira ação < 30s

---

# 9. RISCOS DA FASE

| Risco | Probabilidade | Impacto | Mitigação |
|-------|---------------|---------|-----------|
| Rejeição Apple App Store | alta | alto | revisar guidelines obsessivamente; preparar conteúdo médico legal; resposta rápida |
| Push para iOS APNs complicado | alta | médio | usar Expo Push como abstração |
| Telemedicina WebRTC instável | alta | crítico | fallback para Google Meet/Zoom; iterar |
| OTA breaks por SDK update | média | médio | testar em canary antes de full rollout |
| Adoção lenta (pacientes preferem web) | alta | médio | onboarding educativo; vantagens só do mobile (push, biometria) |
| Custo Apple Developer ($99/ano) + Google ($25 uma vez) | baixa | baixo | embutido no orçamento |
| Compartilhamento entre web e mobile vira caos | alta | alto | monorepo bem desenhado; types como contrato |

---

# 10. ANTI-METAS

NÃO fazer na Fase 4:

* desktop nativo (não há demanda real)
* watchOS / wearable (Fase 5+ talvez)
* completamente offline (apenas básico)
* sem dependência de internet (irrealista para SaaS)
* feature parity 100% com web (mobile prioriza top 10 ações)

---

# 11. CUSTOS DA FASE

| Item | Custo |
|------|-------|
| Apple Developer Account | $99/ano |
| Google Play Console | $25 (uma vez) |
| Expo EAS Build | $99/mês (free tier limita) |
| Designer mobile | R$ 5.000–10.000 |
| Dev RN (parcial ou contratado) | R$ 15.000–25.000/mês × 3 |
| Telemedicina (Daily.co / Twilio) | $50–200/mês inicial |
| FCM | R$ 0 (free) |
| Sentry React Native | incluído no plano existente |
| **Total adicional (3–4 meses)** | **~R$ 55.000–100.000** |

---

# 12. CRITÉRIO DE PRONTO / HANDOFF

Fase 4 termina quando:

* [ ] App Paciente publicado e estável (rating > 4.3)
* [ ] App Profissional publicado e estável
* [ ] adoção > 40% dos pacientes ativos
* [ ] telemedicina operacional em produção
* [ ] crash-free > 99.5%

Próximo: **Fase 5 (Plataforma)** para expandir ecossistema.

---

# 13. PARALELISMO COM TRACKS

* **Go-to-market** — apps viram diferencial de venda; pesquisa qualitativa com pacientes; mídia regional (cobertura local)
* **Cadência operacional** — release train de mobile (semanal OTA, mensal store); QA dedicado em devices; suporte específico para mobile

---

# 14. REFERÊNCIAS

* `docs/mobile.md` — especificação técnica
* `ADRs/004-nextjs.md` (compartilhamento de stack web)
* `docs/auth.md`
* `docs/portal-paciente.md`
* `docs/whatsapp.md`
* `docs/roadmap/06-inteligencia.md` (fase anterior)
* `docs/roadmap/08-plataforma.md` (próxima fase)
