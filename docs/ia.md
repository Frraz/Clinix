# IA — Clinix

> Estratégia de inteligência artificial do Clinix.

---

# 1. VISÃO GERAL

O Clinix nasce preparado para IA.

A IA do Clinix NÃO é marketing.

Deve ser:

* útil
* segura
* auditável
* opcional
* explicável
* respeitosa à autonomia clínica

---

# 2. FILOSOFIA

A IA do Clinix:

* economiza tempo do profissional
* nunca decide por ele
* sempre exige aprovação humana em ações críticas
* nunca substitui validação clínica
* nunca expõe dados entre tenants
* respeita LGPD rigorosamente

---

# 3. OBJETIVOS

A IA deve permitir:

* resumir prontuários longos
* sugerir CID
* preencher SOAP rapidamente
* prever no-show
* prever inadimplência
* responder dúvidas operacionais
* analisar gargalos
* automatizar tarefas repetitivas

---

# 4. CATEGORIAS DE IA

## IA Clínica

Apoio à decisão e produtividade clínica.

---

## IA Operacional

Previsão e otimização da operação.

---

## IA Conversacional

Assistentes internos e busca inteligente.

---

# 5. STACK

Tooling planejado:

* Claude API (Anthropic) — clínica e conversacional
* OpenAI API — fallback e embeddings
* LangChain — orquestração e RAG
* scikit-learn — modelos leves operacionais
* pgvector no PostgreSQL — embeddings de prontuário

---

# 6. ESTRATÉGIA DE FASE

## Fase 3a — IA Clínica

* resumo automático de prontuário antes da consulta
* sugestão de CID-10 baseada na evolução
* pré-preenchimento de SOAP via dictation
* sugestão de prescrições recorrentes

---

## Fase 3b — IA Operacional

* previsão de no-show
* previsão de inadimplência
* análise de gargalos operacionais
* sugestão de horários para reativação

---

## Fase 3c — IA Conversacional

* assistente interno (recepção, gestor)
* chatbot do portal do paciente
* busca natural por histórico clínico
* automação por linguagem natural

---

# 7. RESUMO DE PRONTUÁRIO

Caso de uso de alto valor:

* paciente com 4 anos de histórico
* IA gera resumo em 20s antes da consulta
* destaca alergias, doenças crônicas, medicações
* exibe alertas críticos

---

# 8. SUGESTÃO DE CID

* IA analisa a evolução
* sugere CID-10 ranqueados
* médico sempre aprova
* registro de aceitação/rejeição em audit log

---

# 9. SOAP ASSISTIDO

* médico dita ou digita rascunho
* IA estrutura em S-O-A-P
* médico revisa e edita
* versão final assinada digitalmente

---

# 10. ALERTAS CLÍNICOS INTELIGENTES

A IA pode:

* detectar interação medicamentosa
* alertar exame vencido
* alertar retorno atrasado
* destacar risco baseado em histórico

Sem nunca:

* tomar a decisão
* mascarar incertezas
* substituir o profissional

---

# 11. PRÉ-ANAMNESE

Paciente responde antes da consulta:

* IA organiza
* IA classifica prioridade
* IA gera resumo pré-consulta
* médico recebe pronto

---

# 12. PREVISÃO DE NO-SHOW

Modelo leve em scikit-learn:

* features: histórico, distância, especialidade, horário, clima
* score de probabilidade
* aciona automação preventiva

---

# 13. PREVISÃO DE INADIMPLÊNCIA

Modelo similar:

* score por cobrança
* aciona cobrança preventiva
* não nega atendimento

---

# 14. ASSISTENTE OPERACIONAL

Exemplos de uso:

* "quais pacientes faltaram 2x este mês?"
* "qual o faturamento da Dra. Ana em maio?"
* "quantos retornos atrasados temos?"

Resposta via NLP + query estruturada.

---

# 15. RAG SOBRE PRONTUÁRIOS

Para busca semântica clínica:

* embeddings de evoluções via pgvector
* nunca cruza tenants
* nunca expõe outros pacientes
* sempre auditado

---

# 16. PRIVACIDADE E LGPD

Regras obrigatórias:

* dados nunca usados para treinamento de modelos externos
* providers com BAA/DPA assinado
* anonimização sempre que possível
* opt-out por tenant
* opt-out por paciente

---

# 17. PROVIDERS

Selecionar providers que:

* não treinem com inputs
* sigam SOC2 / ISO 27001
* aceitem residência de dados quando necessário
* aceitem retenção zero

---

# 18. CUSTOS

Controle obrigatório de custos:

* limite por tenant
* limite por plano
* logging de tokens consumidos
* alerta de uso anômalo

---

# 19. CACHE DE IA

Respostas comuns devem ser cacheadas:

* sugestões de CID por evolução similar
* resumos de prontuário (até nova alteração)
* embeddings calculados uma vez

---

# 20. PROMPTS

Padrão de engenharia de prompts:

* prompts versionados em código
* nunca expostos no frontend
* sistema próprio para A/B testing
* avaliação humana de qualidade

---

# 21. AVALIAÇÃO

Toda feature de IA deve possuir:

* métricas de qualidade
* feedback do usuário (👍/👎)
* taxa de aceitação
* tempo economizado estimado

---

# 22. SEGURANÇA

Toda chamada de IA deve:

* validar tenant
* validar permissões
* registrar em audit log
* respeitar rate limit
* nunca incluir dados de outros pacientes no contexto

---

# 23. PROMPT INJECTION

Proteções obrigatórias:

* sanitização de inputs
* separação clara entre instruções e dados
* validação estruturada de outputs
* nunca executar ações automaticamente baseadas em output de IA livre

---

# 24. EXPLICABILIDADE

Toda sugestão de IA deve:

* mostrar a fonte (qual trecho do prontuário)
* mostrar a confiança
* permitir rejeição
* registrar a decisão

---

# 25. FEEDBACK LOOP

Aceitação/rejeição é registrada:

* alimenta métricas
* alimenta melhorias futuras
* nunca alimenta treinamento sem consentimento

---

# 26. O QUE EVITAR

Nunca permitir:

* IA decidindo prescrição sozinha
* IA fechando diagnóstico
* IA ocultando incerteza
* IA cruzando dados entre tenants
* IA enviada para provider sem DPA
* IA sem auditoria

---

# 27. OBJETIVO FINAL

Construir uma camada de IA:

* útil de verdade
* segura
* auditável
* explicável
* respeitosa à autonomia clínica
* economicamente viável
* preparada para evolução contínua
