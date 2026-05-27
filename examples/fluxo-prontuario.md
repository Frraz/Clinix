# Fluxo de Prontuário — Clinix

> Jornada completa de uso do prontuário eletrônico durante o atendimento.

---

# 1. PERSONAS

* profissional clínico (médico, dentista, fisio, psico, biomédico)
* assistente clínico (quando aplicável)
* sistema (IA assistiva, automações)

---

# 2. PRÉ-CONSULTA

## Abertura do atendimento

* paciente chamado da fila
* prontuário abre automaticamente
* timeline clínica carregada
* alertas críticos exibidos no topo

---

## Alertas exibidos

* alergias
* doenças crônicas
* medicações em uso
* observações clínicas críticas
* exames vencidos

---

## Resumo IA (Fase 3)

* botão "Resumir histórico"
* IA gera resumo em ~20s
* profissional revisa antes de iniciar

---

# 3. ANAMNESE

## Anamnese estruturada

* template por especialidade
* campos dinâmicos (`MedicalRecord.content` JSONB)
* preenchimento progressivo
* validação de campos obrigatórios

---

## Anamnese livre

* campo de texto rico (TipTap)
* histórico de versões
* salvamento automático

---

# 4. EVOLUÇÃO SOAP

Padrão estruturado:

* **S** — Subjetivo (queixa, história)
* **O** — Objetivo (exame físico, sinais vitais)
* **A** — Avaliação (diagnóstico, hipóteses)
* **P** — Plano (conduta, prescrição, retorno)

---

## SOAP assistido (Fase 3)

* profissional dita ou digita rascunho
* IA estrutura em S-O-A-P
* profissional revisa
* assinatura digital

---

# 5. CID

* busca rápida por termo
* sugestões IA baseadas na evolução (Fase 3)
* múltiplos CIDs por evolução
* histórico de CIDs do paciente

---

# 6. PRESCRIÇÕES

## Criar prescrição

* busca de medicamento
* dose, frequência, duração
* observações
* validade

---

## Prescrição recorrente

* sugestões baseadas em prescrições anteriores
* aplicar com 1 clique

---

## Saída

* PDF gerado via WeasyPrint
* assinado digitalmente
* enviado por WhatsApp/Email
* armazenado no prontuário
* compartilhado com farmácia (futuro)

---

# 7. SOLICITAÇÃO DE EXAMES

## Pedido

* seleção de exames
* envio direto ao laboratório (interno ou externo)
* impressão ou envio digital

---

## Acompanhamento

* timeline mostra status
* notificação ao liberar resultado
* resultado vinculado à evolução

Ver `examples/fluxo-laboratorio.md`.

---

# 8. ATESTADOS E DOCUMENTOS

Documentos gerados:

* atestado médico
* declaração de comparecimento
* relatório clínico
* termo de consentimento
* encaminhamento

Todos:

* via templates configuráveis
* PDFs assinados
* armazenados no prontuário
* compartilháveis com TTL

---

# 9. ANEXOS

## Upload

* imagens clínicas (fotos, exames externos)
* PDFs
* DICOM (futuro)

---

## Validação

* mime-type
* tamanho máximo (por plano)
* antivirus scan (futuro)

---

## Visualização

* preview inline
* lupa em imagens
* comparação lado a lado (Fase 3)

---

# 10. ODONTOGRAMA

Quando a especialidade é odontologia:

* odontograma visual aparece
* registro por dente e face
* histórico de procedimentos por dente
* planejamento ortodôntico

---

# 11. EVOLUÇÃO FISIOTERÁPICA

Recursos específicos:

* avaliação funcional inicial
* metas terapêuticas
* sessões numeradas
* evolução por sessão
* alta com relatório

---

# 12. EVOLUÇÃO PSICOLÓGICA

Recursos específicos:

* anotações com sigilo reforçado
* acesso restrito por permissão de objeto (django-guardian)
* nunca visível para outros profissionais sem autorização
* audit reforçado

---

# 13. ASSINATURA DIGITAL

Toda evolução crítica:

* assinada com certificado do profissional
* timestamp registrado
* hash armazenado em audit
* validade jurídica preservada

---

# 14. VERSIONAMENTO

Toda alteração:

* versão anterior preservada
* diff disponível para visualização
* nome do editor registrado
* motivo da edição quando aplicável

---

# 15. TIMELINE CLÍNICA

Cronologia unificada:

* consultas
* evoluções
* prescrições
* exames
* documentos
* mensagens enviadas
* eventos importantes

Filtros por:

* tipo
* período
* profissional
* especialidade

---

# 16. BUSCA NO PRONTUÁRIO

* busca textual em todas as evoluções
* full-text search (português)
* busca por CID
* busca por medicamento
* busca por exame

---

# 17. PERMISSÕES

| Ação | Quem |
|------|------|
| Visualizar prontuário | profissional do tenant + responsável pelo paciente |
| Criar evolução | profissional clínico |
| Editar evolução própria | profissional autor (com versão) |
| Visualizar prontuário psicológico | apenas psicólogo do paciente |
| Hard delete | bloqueado (apenas anonimização via LGPD) |

---

# 18. AUDITORIA

Registrar:

* visualizações do prontuário
* criação de evolução
* edição (com diff)
* prescrição emitida
* exame solicitado
* documento gerado
* compartilhamento externo

---

# 19. PERFORMANCE

* timeline paginada
* anexos lazy-loaded
* índice no Postgres por `(patient_id, created_at)`
* cache de resumos (invalidado em update)

---

# 20. EVENTOS PUBLICADOS

* `medical_record.created`
* `medical_record.updated`
* `prescription.issued`
* `exam.requested`
* `document.generated`

Alimentam workflows, CRM e analytics.

---

# 21. PÓS-CONSULTA AUTOMÁTICA

Após `appointment.completed`:

* receita enviada por email/WhatsApp
* orientações enviadas
* solicitação de retorno agendada
* NPS após 7 dias

---

# 22. O QUE EVITAR

* edição sem versionamento
* exclusão definitiva sem fluxo LGPD
* prontuário sem assinatura
* compartilhamento sem TTL
* exposição entre tenants
* acesso de psicologia sem autorização explícita

---

# 23. OBJETIVO

Prontuário que:

* economiza tempo do profissional
* preserva histórico completo
* respeita LGPD e CFM rigorosamente
* permite IA assistiva sem substituir decisão
* serve como prova jurídica defensável
