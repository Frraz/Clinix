# Mobile — Clinix

> Estratégia, arquitetura e diretrizes dos aplicativos mobile do Clinix.

---

# 1. VISÃO GERAL

O Clinix é web-first no MVP, mas a arquitetura é mobile-ready desde o dia 1.

O mobile virá como Fase 2.

---

# 2. ESTRATÉGIA

NÃO criar app mobile no MVP.

Preparar arquitetura para mobile.

Quando entrar:

* o backend já estará pronto (API-first)
* o frontend web já validou os fluxos críticos
* a stack mobile reaproveita lógica e tipos do web

---

# 3. STACK OFICIAL

* React Native
* Expo SDK 52+
* Expo Router (file-based, igual ao Next.js)
* TypeScript
* TanStack Query v5 (mesmo padrão do web)
* React Hook Form + Zod
* React Native MMKV (storage local)
* Expo Notifications + FCM

---

# 4. POR QUE REACT NATIVE + EXPO

* compartilha tipos TypeScript com o web
* compartilha hooks de API
* compartilha schemas Zod
* iOS e Android com um único código
* EAS Build na nuvem (sem Mac local)
* OTA updates para correções rápidas
* mesma equipe atende web e mobile

---

# 5. TRÊS APPS PLANEJADOS

## App Profissional

* agenda
* prontuário
* evolução rápida
* telemedicina
* alertas
* assinatura digital

---

## App Paciente

* agendamento
* exames e laudos
* histórico
* pagamentos
* chat com a clínica
* pré-anamnese

---

## App Gestor

* KPIs
* financeiro
* produtividade por unidade
* alertas operacionais

---

# 6. AUTENTICAÇÃO MOBILE

* JWT (mesmo backend)
* refresh token em MMKV criptografado
* biometria nativa (Face ID, Touch ID, biometria Android)
* logout remoto via blacklist do refresh token

---

# 7. NOTIFICAÇÕES PUSH

Stack:

* Expo Notifications (camada)
* Firebase Cloud Messaging (Android)
* APNs (iOS, via Expo)

---

## Eventos típicos

* lembrete de consulta
* confirmação de consulta
* exame liberado
* nova mensagem do WhatsApp na clínica
* cobrança pendente

---

# 8. ARMAZENAMENTO LOCAL

React Native MMKV para:

* tokens
* preferências
* cache de listas pequenas
* dados offline mínimos

Nunca armazenar:

* dados clínicos completos
* documentos sensíveis em texto claro

---

# 9. OFFLINE FUTURO

Arquitetura preparada para:

* cache de agenda do dia
* fila de envio de evoluções
* sync ao reconectar
* indicador de offline

(Implementação fase 2.5+.)

---

# 10. UX MOBILE

Princípios:

* gestos naturais
* ações com 1 toque
* layouts adaptáveis
* leitura confortável
* fontes acessíveis

---

# 11. UI MOBILE

* design system compartilhado com web (tokens)
* componentes nativos quando necessário
* respeitar padrões iOS e Android
* dark mode nativo

---

# 12. CÂMERA E SCAN

Recursos nativos:

* câmera para anexar exames
* OCR de documentos
* leitura de QR Code (check-in, amostras)
* upload de fotos clínicas

---

# 13. TELEMEDICINA

Vídeo via:

* WebRTC nativo
* fallback para web view
* gravação opcional
* compartilhamento de tela

---

# 14. ASSINATURA DIGITAL MOBILE

* assinatura biométrica
* integração com certificado em nuvem
* assinatura de receita médica

---

# 15. SEGURANÇA MOBILE

* SSL pinning
* root/jailbreak detection
* obfuscação de bundle
* secrets nunca no client
* biometria para reabertura

---

# 16. PUSH SEGURO

* payload sem dados sensíveis
* mensagens neutras ("Nova mensagem", não conteúdo)
* deep link autenticado

---

# 17. BUILD E DEPLOY

* EAS Build (Expo Application Services)
* canais: development, preview, production
* OTA updates via Expo Updates
* code push controlado

---

# 18. APP STORE

Submissão:

* App Store Connect (iOS)
* Google Play Console (Android)
* política LGPD declarada
* política de IA declarada (quando aplicar)

---

# 19. INTERNACIONALIZAÇÃO

PT-BR default.

Arquitetura preparada para:

* en-US
* es-AR / es-MX

---

# 20. ACESSIBILIDADE

Mobile deve seguir:

* VoiceOver (iOS)
* TalkBack (Android)
* contraste WCAG AA
* tap targets ≥ 44pt
* gestos alternativos

---

# 21. PERFORMANCE

Metas:

* cold start < 2s
* tela inicial < 1s após cold start
* navegação fluida 60fps
* uso de memória controlado

---

# 22. MONITORAMENTO

* Sentry para erros
* Expo Application Services para crashes
* analytics de uso (sem PII)

---

# 23. INTEGRAÇÃO COM O WEB

App mobile e web compartilham:

* mesma API
* mesmos tipos TypeScript
* mesmas regras de validação Zod
* mesmo design system (tokens)

---

# 24. O QUE EVITAR

Nunca permitir:

* lógica de negócio replicada
* APIs específicas para mobile (sempre genéricas)
* dados sensíveis em texto claro local
* token sem expiração
* push com payload sensível

---

# 25. OBJETIVO FINAL

Construir uma camada mobile:

* nativa
* fluida
* segura
* reutilizando ao máximo o web
* lançada apenas após o web estar maduro
* preparada para crescer com o produto
