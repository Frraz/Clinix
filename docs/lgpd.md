# LGPD — Clinix

> Estratégia de conformidade com a Lei Geral de Proteção de Dados no Clinix.

---

# 1. VISÃO GERAL

O Clinix manipula dados pessoais sensíveis (dados de saúde, art. 5º, II da LGPD).

A conformidade LGPD não é opcional.

É requisito legal e diferencial competitivo.

---

# 2. PAPÉIS

Na arquitetura do Clinix:

* **Clinix (empresa SaaS)** → operador (art. 5º, VII)
* **Clínica cliente** → controlador (art. 5º, VI)
* **Paciente** → titular (art. 5º, V)

---

# 3. BASES LEGAIS APLICÁVEIS

Principais bases legais usadas:

* execução de contrato (atendimento ao paciente)
* cumprimento de obrigação legal/regulatória (CFM, CFO, CFP)
* exercício regular de direitos
* tutela da saúde (art. 11, II, f)
* consentimento (marketing, comunicação opcional)

---

# 4. OBJETIVOS

Garantir:

* respeito aos direitos dos titulares
* transparência
* segurança dos dados
* rastreabilidade
* prova de conformidade
* defesa em caso de incidente

---

# 5. DIREITOS DOS TITULARES

Pacientes podem solicitar (art. 18):

* confirmação de tratamento
* acesso aos dados
* correção
* anonimização
* portabilidade
* eliminação dos dados tratados com consentimento
* informação sobre compartilhamento
* revogação do consentimento

---

# 6. IMPLEMENTAÇÃO TÉCNICA

## Modelos obrigatórios

* `ConsentRecord` — consentimentos por finalidade
* `DataProcessingLog` — log de acesso a dados pessoais sensíveis
* `AnonymizationJob` — tarefa de anonimização sob demanda
* `DeletionRequest` — pedido de exclusão (direito ao esquecimento)
* `PrivacyPolicy` — versões aceitas pelo paciente
* `DataExportJob` — portabilidade

---

# 7. CONSENTIMENTOS

Cada consentimento registra:

* paciente
* finalidade específica
* versão da política de privacidade
* canal de coleta
* timestamp
* revogação se aplicável

---

# 8. FINALIDADES TÍPICAS

* atendimento clínico (base legal: tutela da saúde)
* comunicação operacional (base: execução de contrato)
* marketing/CRM (base: consentimento, revogável)
* IA assistiva (base: consentimento explícito)
* compartilhamento com convênio (base: execução de contrato)

---

# 9. PORTAL DO TITULAR

O paciente deve ter:

* acesso aos próprios dados
* download em formato estruturado
* solicitação de correção
* solicitação de exclusão
* visualização dos consentimentos
* revogação a qualquer momento

---

# 10. PORTAL DO DPO

A clínica deve ter:

* dashboard de solicitações
* prazos legais cronometrados
* histórico de incidentes
* relatórios de conformidade
* gestão de consentimentos

---

# 11. PRAZOS LEGAIS

Atendimento de solicitações:

* confirmação/acesso: 15 dias
* correção: imediato
* anonimização/eliminação: prazo combinado, máx. legal
* portabilidade: 15 dias

Cronômetro automático no painel do DPO.

---

# 12. ANONIMIZAÇÃO

Processo controlado:

* identifica registros relacionados
* substitui PII por hash não reversível
* preserva integridade estatística
* mantém dados clínicos para retenção legal (CFM)
* registra operação em audit

---

# 13. EXCLUSÃO

Eliminação respeita:

* obrigação legal de retenção (CFM: 20 anos)
* dados clínicos: anonimização ao invés de exclusão
* dados comerciais: exclusão imediata se possível
* notificação ao titular após operação

---

# 14. PORTABILIDADE

Export deve fornecer:

* PDF legível
* JSON estruturado
* todos os dados do paciente
* histórico clínico
* documentos
* prazo: 15 dias

---

# 15. RETENÇÃO DE DADOS

Política por categoria:

* prontuário: 20 anos (CFM 1.821/2007)
* exames: 20 anos
* dados financeiros: 5 anos
* dados comerciais: 5 anos após última interação
* logs de acesso: 6 meses
* logs de segurança: 2 anos

---

# 16. CRIPTOGRAFIA

Em repouso:

* CPF, RG, dados sensíveis: AES-256
* backups: criptografados
* storage (R2): server-side encryption

Em trânsito:

* HTTPS obrigatório
* TLS 1.2+
* certificados válidos

---

# 17. CONTROLE DE ACESSO

* RBAC rigoroso
* princípio do menor privilégio
* MFA obrigatório para admin/clínico
* sessões com expiração
* logout automático

---

# 18. AUDIT DE ACESSO

Todo acesso a dado sensível registra:

* quem acessou
* qual dado
* quando
* de onde
* request_id
* duração da visualização

---

# 19. INCIDENTES

Plano de resposta a incidentes:

* identificação
* contenção
* erradicação
* recuperação
* lições aprendidas
* comunicação à ANPD em até 2 dias úteis se aplicável
* comunicação aos titulares afetados

---

# 20. COMUNICAÇÃO DE INCIDENTE

Deve conter (art. 48):

* descrição
* informações afetadas
* medidas de segurança aplicadas
* riscos
* medidas tomadas
* medidas recomendadas ao titular

---

# 21. DPO

Cada tenant deve ter um DPO designado.

O sistema deve oferecer:

* campo de cadastro do DPO
* canal direto exposto no portal do paciente
* dashboard exclusivo do DPO

---

# 22. CONTRATO DE OPERADOR

Toda clínica assina:

* contrato SaaS
* termo de tratamento de dados
* matriz de responsabilidades
* SLA de incidentes

---

# 23. SUB-OPERADORES

Operadores terceiros do Clinix:

* AWS / Cloudflare R2 (storage)
* Hostinger (infra)
* Stripe / Mercado Pago (pagamentos)
* Resend (email)
* Evolution API (WhatsApp)
* Anthropic / OpenAI (IA, opcional)

Todos com DPA assinado.

---

# 24. RIPD

Relatório de Impacto à Proteção de Dados:

* obrigatório quando há alto risco
* aplicar para IA clínica
* aplicar para integrações sensíveis
* documentado e versionado

---

# 25. DADOS DE MENORES

Tratamento especial:

* consentimento dos responsáveis
* dados separados quando aplicável
* acesso restrito
* logs reforçados

---

# 26. COOKIES E TRACKING

Site público respeita:

* banner de cookies
* opt-in para não essenciais
* política transparente

---

# 27. PRIVACY BY DESIGN

Toda nova feature passa por checklist:

* qual dado é coletado?
* qual a base legal?
* qual a finalidade?
* qual o tempo de retenção?
* quem terá acesso?
* há risco aos titulares?

---

# 28. PRIVACY BY DEFAULT

Configurações padrão:

* compartilhamento mínimo
* coleta mínima
* visibilidade mínima
* opt-in para extras

---

# 29. TRANSFERÊNCIA INTERNACIONAL

Quando dados saem do Brasil:

* base legal documentada
* país com nível adequado OU cláusulas contratuais
* registro no RIPD
* informação ao titular

---

# 30. TREINAMENTO

Time interno do Clinix deve receber:

* treinamento LGPD na entrada
* reciclagem anual
* simulações de incidentes
* política interna clara

---

# 31. AUDITORIA EXTERNA

Roadmap:

* auditoria LGPD anual
* certificação ISO 27001 (futuro)
* certificação SOC 2 (futuro)

---

# 32. O QUE EVITAR

Nunca permitir:

* coleta sem finalidade declarada
* compartilhamento sem base legal
* exposição de dado sensível
* acesso sem registro
* exclusão sem auditoria
* armazenamento eterno sem justificativa

---

# 33. OBJETIVO FINAL

Construir um produto:

* conforme à LGPD
* respeitoso aos titulares
* defensável em auditoria
* preparado para fiscalização da ANPD
* diferenciado por confiança

LGPD não é checkbox.

É como o Clinix demonstra respeito pelo paciente.
