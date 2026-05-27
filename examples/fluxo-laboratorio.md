# Fluxo de Laboratório — Clinix

> Jornada completa de um exame, desde a solicitação até a entrega do laudo.

---

# 1. ESCOPO

Cobre todo o fluxo laboratorial:

* solicitação
* coleta
* triagem
* processamento
* validação
* liberação
* entrega
* visualização no prontuário

Para arquitetura geral ver `docs/laboratorio.md`.

---

# 2. PERSONAS

* profissional solicitante (médico)
* atendente (recepção do laboratório)
* coletor
* técnico (biomédico)
* responsável técnico (validação clínica)
* paciente
* sistema (automações, notificações)

---

# 3. ORIGEM DA SOLICITAÇÃO

* prontuário (profissional interno do tenant)
* encaminhamento externo (paciente chega com pedido)
* convênio
* integração externa (futuro)

---

# 4. ETAPA 1 — SOLICITAÇÃO

## Dados obrigatórios

* paciente
* exames solicitados
* solicitante
* observações clínicas
* prioridade (rotina, urgente, alta complexidade)
* convênio (quando aplicável)

---

## Eventos

* `lab_order.created`
* timeline do paciente atualizada

---

# 5. ETAPA 2 — AGENDAMENTO DE COLETA

Quando o exame exige coleta presencial ou domiciliar:

* paciente seleciona horário disponível
* notificação por WhatsApp
* lembrete 24h antes
* lembrete 2h antes

---

# 6. ETAPA 3 — COLETA

## Na unidade

* check-in do paciente
* leitura do código do pedido
* impressão das etiquetas das amostras
* coleta
* registro do coletor responsável
* registro do horário

---

## Domiciliar

* coletor recebe ordem no app (futuro)
* GPS confirma chegada
* assinatura digital do paciente
* foto de envio (opcional)

---

## Eventos

* `lab_sample.collected`

---

# 7. ETIQUETAS E CÓDIGO DE BARRAS

Cada amostra gera:

* código único (barcode/QR)
* etiqueta impressa imediatamente
* leitura obrigatória em cada etapa seguinte

Rastreamento ponto a ponto evita troca de amostras.

---

# 8. ETAPA 4 — TRIAGEM

Recepção das amostras no laboratório:

* leitura de barcode
* validação de integridade visual
* registro do recebedor
* alocação na fila de processamento por área

---

## Áreas típicas

* hematologia
* bioquímica
* microbiologia
* uroanálise
* hormônios
* imunologia
* genética (futuro)

---

# 9. ETAPA 5 — PROCESSAMENTO

## Manual

* técnico assume amostra
* registra início
* executa análise
* registra resultado

---

## Automático (futuro)

* integração HL7/ASTM com equipamentos
* import direto do resultado
* validação técnica em seguida

---

## SLA

* cada exame tem tempo esperado
* dashboard mostra atrasos
* alerta interno quando ultrapassa SLA

---

# 10. ETAPA 6 — VALIDAÇÃO TÉCNICA

* técnico revisa resultado
* compara com valores de referência
* sinaliza valores críticos
* pode solicitar repetição

---

# 11. ETAPA 7 — VALIDAÇÃO CLÍNICA

Responsável técnico:

* revisa todos os exames do pedido
* aprova consolidação
* adiciona observações se necessário

---

# 12. ETAPA 8 — LIBERAÇÃO

Só ocorre quando:

* todos os exames validados
* responsável técnico identificado
* assinatura digital aplicada (ICP-Brasil preferencial)

---

## Eventos

* `lab_result.released`
* `exam.released` (compatibilidade com prontuário)

---

# 13. ETAPA 9 — ENTREGA AO PACIENTE

## Notificação automática

Workflow:

```yaml
trigger: lab_result.released
actions:
  - action: whatsapp.send_template
    template: "exame_disponivel"
  - action: email.send
    template: "exame_disponivel"
```

---

## Mensagem

```
{{patient_name}}, seu exame está disponível!

Acesse com segurança:
{{secure_link}}

O link expira em 7 dias.
```

---

# 14. ETAPA 10 — VISUALIZAÇÃO NO PRONTUÁRIO

Profissional solicitante:

* recebe notificação
* visualiza resultado integrado ao prontuário
* compara com histórico
* anota evolução baseada no resultado

---

# 15. VALORES CRÍTICOS

Quando um exame retorna fora de faixa crítica:

* alerta vermelho no laudo
* notificação ao solicitante por WhatsApp/email
* registro em audit
* opção de contato imediato com o paciente

---

# 16. REPETIÇÃO

Quando necessário:

* nova coleta agendada
* novo pedido vinculado ao original
* paciente notificado
* histórico preservado

---

# 17. LAUDO RETIFICADO

Caso erro identificado após liberação:

* versão original NUNCA é apagada
* nova versão criada
* destaque visual de retificação
* paciente notificado proativamente
* solicitante notificado
* audit completo

---

# 18. CONVÊNIOS

Quando coberto:

* tabela do convênio aplicada
* envio TISS
* aguarda glosa
* repasse ao laboratório

---

# 19. FATURAMENTO

* particular: cobrança gerada na liberação
* convênio: faturamento pelo TISS

Ver `examples/fluxo-financeiro.md`.

---

# 20. DASHBOARD LABORATORIAL

KPIs essenciais:

* pedidos do dia
* amostras em processamento
* exames atrasados (SLA)
* tempo médio por etapa
* produtividade por técnico
* receita por área
* exames mais solicitados
* valores críticos do dia

---

# 21. PERMISSÕES

| Ação | Quem |
|------|------|
| Criar pedido | profissional clínico |
| Realizar coleta | coletor, técnico |
| Triagem | recepção do laboratório |
| Processar | técnico (biomédico) |
| Validar clinicamente | responsável técnico |
| Liberar laudo | responsável técnico |
| Visualizar resultado | profissional solicitante + paciente |
| Retificar laudo | responsável técnico (com audit reforçado) |

---

# 22. AUDITORIA

Registrar:

* quem coletou
* quem triou
* quem processou
* quem validou tecnicamente
* quem validou clinicamente
* quem liberou
* quem visualizou
* qualquer retificação
* qualquer compartilhamento externo

---

# 23. EVENTOS PUBLICADOS

* `lab_order.created`
* `lab_sample.collected`
* `lab_sample.triaged`
* `lab_sample.processing`
* `lab_result.validated`
* `lab_result.released`
* `lab_result.rectified`

Alimentam workflows, notificações e analytics.

---

# 24. INTEGRAÇÕES FUTURAS

* equipamentos via HL7/ASTM
* DICOM (imagem)
* PACS
* laboratórios parceiros (terceirização de análise)
* TISS

---

# 25. SEGURANÇA

* RLS no PostgreSQL para `lab_results`
* URL assinada com TTL para download
* nunca expor laudo sem autenticação
* nunca cruzar amostras entre tenants
* assinatura digital com hash em audit

---

# 26. O QUE EVITAR

* perda de rastreabilidade de amostra
* laudo sem assinatura
* alteração silenciosa de resultado
* notificação ao paciente antes da liberação clínica
* compartilhamento entre tenants
* exposição de resultado sem TTL

---

# 27. OBJETIVO

Fluxo laboratorial que:

* rastreia cada amostra ponto a ponto
* respeita SLA por exame
* libera laudos com validade jurídica
* entrega resultados ao paciente com UX moderna
* alimenta o prontuário automaticamente
* serve como diferencial em laboratórios independentes
