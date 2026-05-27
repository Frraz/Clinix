# Compliance com Conselhos Profissionais — Clinix

> Conformidade com CFM, CFO, CFP, CFBM, CREFITO, CRN e demais conselhos de saúde.

---

# 1. VISÃO GERAL

O Clinix atende profissionais regulados:

* médicos (CFM/CRM)
* dentistas (CFO/CRO)
* psicólogos (CFP/CRP)
* fisioterapeutas (COFFITO/CREFITO)
* biomédicos (CFBM/CRBM)
* nutricionistas (CFN/CRN)
* enfermeiros (COFEN/COREN)

Cada conselho tem regras próprias sobre:

* prontuário
* prescrição
* sigilo profissional
* assinatura
* retenção de dados
* publicidade

O Clinix deve respeitar todas.

---

# 2. OBJETIVOS

Garantir:

* defensabilidade jurídica do uso do sistema
* conformidade com normas vigentes
* atualização contínua conforme normas evoluem
* documentação rastreável para fiscalização
* respeito ao sigilo profissional

---

# 3. PRINCÍPIOS

* compliance NÃO é checkbox
* normas evoluem, o sistema deve evoluir junto
* responsabilidade compartilhada: Clinix oferece ferramenta, profissional usa
* documentação clara do que o Clinix cobre
* documentação clara do que é responsabilidade do profissional

---

# 4. NORMAS PRINCIPAIS

## CFM

* **Resolução CFM 1.821/2007** — prontuário eletrônico (substituída em parte por 1.638/2002, 2.227/2018)
* **Resolução CFM 2.314/2022** — telemedicina
* **Código de Ética Médica** (Resolução CFM 2.217/2018)
* **Manual de Certificação para Sistemas de Registro Eletrônico em Saúde (S-RES)** — SBIS/CFM

---

## CFO

* **Código de Ética Odontológica** (Resolução CFO 118/2012)
* normas sobre prontuário odontológico
* odontograma como item obrigatório
* radiografias e imagens

---

## CFP

* **Resolução CFP 06/2019** — registros documentais
* sigilo profissional rigoroso
* dados psicológicos com nível extra de proteção
* anotações privadas do psicólogo

---

## COFFITO

* registros de avaliação e evolução
* prescrição fisioterapêutica
* alta com relatório

---

## CFBM

* laudos de exames com responsável técnico
* assinatura digital quando aplicável

---

## CFN

* registros nutricionais
* prescrição de conduta nutricional

---

# 5. PRONTUÁRIO

## Exigências comuns

* identificação completa do paciente
* identificação do profissional
* data e hora
* anamnese
* exame físico
* hipóteses diagnósticas
* conduta
* assinatura

---

## Inalterabilidade

* edição preserva versão anterior (versionamento)
* anotação adicional ao invés de sobrescrita quando aplicável
* timestamp em cada versão
* identificação do editor

---

## Retenção

* CFM: mínimo 20 anos (Resolução 1.821/2007, art. 7º)
* contado a partir do último atendimento
* anonimização permitida após prazos com condições

---

## Assinatura

* certificado digital ICP-Brasil é padrão preferencial
* assinatura interna válida com auditoria robusta como fallback
* SBIS NGS2 (Nível de Garantia de Segurança 2) como referência

---

# 6. PRESCRIÇÃO

## Receita simples

* identificação completa do paciente
* identificação do prescritor
* CRM/CRO/CRN com UF
* medicamento, posologia, duração
* assinatura
* validade

---

## Receita controlada

* modelo específico por categoria (Portaria 344/98)
* B1, B2, C1, C2 com formatos próprios
* numeração de talonário
* retenção pela farmácia
* CFM 1.931/2009 + Anvisa

Clinix pode emitir digitalmente desde que respeite formato legal.

---

## Receita digital

* Lei 14.063/2020 reconhece receita digital
* assinatura digital obrigatória
* QR code de validação
* validade jurídica equivalente à física

---

# 7. SIGILO PROFISSIONAL

## Geral

* todos os dados clínicos sigilosos
* RBAC restrito ao necessário
* compartilhamento exige autorização

---

## Psicologia (CFP)

Nível extra de proteção:

* anotações privadas do psicólogo visíveis APENAS para ele
* compartilhamento NUNCA por padrão
* solicitação judicial registrada e auditada
* anonimização rigorosa em pesquisa
* implementação via `django-guardian` + RLS

Ver `docs/prontuario.md` e `docs/auth.md`.

---

# 8. RESPONSABILIDADE TÉCNICA

Cada estabelecimento tem RT:

* registrado no conselho competente
* dados visíveis em documentos gerados
* nome + número de registro
* assinatura em documentos

Clinix exibe RT em:

* receitas
* atestados
* laudos
* relatórios
* rodapé do portal do paciente

Ver `docs/personalizacao.md`.

---

# 9. CERTIFICAÇÃO SBIS/CFM

## O que é

Manual SBIS/CFM certifica sistemas de prontuário em níveis NGS1 e NGS2.

* NGS1 — controles mínimos
* NGS2 — controles avançados (assinatura ICP-Brasil)

---

## Estratégia do Clinix

* Fase 1-2: alinhar com NGS1 (sem certificação formal)
* Fase 3+: avaliar certificação NGS2 conforme demanda do mercado
* documentar atendimento dos requisitos mesmo sem certificação

---

# 10. PUBLICIDADE MÉDICA

Normas restringem publicidade:

* CFM Resolução 1.974/2011
* CFO normas específicas
* CFP veda exposição comercial

Clinix NÃO oferece marketplace nem promove profissionais publicamente, evitando esse vetor.

Quando entrar campanhas de marketing por WhatsApp/email, respeitar normas do conselho do profissional.

---

# 11. TELEMEDICINA

CFM Resolução 2.314/2022:

* teleconsulta requer relação prévia ou primeira consulta presencial em alguns casos
* prontuário eletrônico obrigatório
* consentimento explícito do paciente
* prescrição digital com assinatura
* gravação opcional (com consentimento)

Aplicar quando módulo entrar (não previsto no momento).

---

# 12. PUBLICAÇÃO E PESQUISA

Dados de paciente para pesquisa:

* anonimização rigorosa
* comitê de ética quando aplicável
* consentimento informado
* Resolução CNS 466/2012 (pesquisa em seres humanos)
* CFM 1.605/2000 (sigilo em pesquisa)

Funcionalidades futuras de IA precisam respeitar isso.

---

# 13. ATESTADOS

CFM Resolução 1.851/2008:

* nome completo do paciente
* tempo concedido
* CID (com consentimento do paciente para divulgação)
* identificação do médico
* assinatura
* carimbo (físico) ou assinatura digital

Templates do Clinix devem atender.

---

# 14. LAUDOS DE EXAMES

CFBM:

* responsável técnico identificado
* CRBM com UF
* assinatura
* metodologia (quando aplicável)
* valores de referência
* observações pertinentes

---

# 15. DOCUMENTOS POR CONSELHO

Cada conselho aceita modelos:

* o Clinix oferece modelos oficiais
* clínica pode personalizar dentro dos limites legais
* alterações que violem normas são bloqueadas

Ver `docs/personalizacao.md`.

---

# 16. AUDITORIA PARA FISCALIZAÇÃO

Quando fiscalizado:

* clínica exporta dados rastreáveis
* sistema fornece histórico de atendimentos
* sistema fornece logs de acesso a prontuário
* sistema fornece evidências de assinatura

Recursos para exportação em `docs/importacao-exportacao.md`.

---

# 17. RETENÇÃO POR CATEGORIA

| Categoria | Prazo mínimo | Norma |
|-----------|--------------|-------|
| Prontuário médico | 20 anos | CFM 1.821/2007 |
| Prontuário odontológico | 20 anos | CFO |
| Prontuário psicológico | 5 anos (após último atendimento) | CFP 06/2019 |
| Prontuário fisioterápico | 10 anos | COFFITO |
| Laudo de exame | 5 anos | CFBM |
| Receita controlada (cópia) | 5 anos | Anvisa |
| Documentos administrativos | 5 anos | geral |

---

# 18. EXCLUSÃO DE DADOS CLÍNICOS

NUNCA exclusão direta:

* anonimização preserva utilidade estatística
* dados anonimizados podem permanecer
* exclusão completa só após prazo legal
* sempre auditada

---

# 19. RESPONSABILIDADE COMPARTILHADA

## Clinix oferece

* ferramentas para registro completo
* templates legalmente revisados
* assinatura digital
* auditoria
* exportação para fiscalização
* atualizações conforme normas evoluem

---

## Profissional é responsável por

* usar corretamente
* preencher prontuário adequadamente
* respeitar sigilo
* respeitar prazos
* responder a fiscalização do próprio conselho

Documentado claramente no termo de uso.

---

# 20. ATUALIZAÇÃO DE NORMAS

Processo:

* monitoramento contínuo de publicações dos conselhos
* avaliação de impacto em até 30 dias após publicação
* implementação prioritária se afeta operação
* comunicação aos clientes
* histórico de mudanças versionado

---

# 21. ASSESSORIA EXTERNA

Recomendado:

* advogado especializado em saúde
* consultor regulatório
* revisão anual de templates
* parecer formal sobre mudanças significativas

---

# 22. ROADMAP DE COMPLIANCE

## Fase 1 (MVP)

* prontuário com versionamento
* assinatura interna com auditoria
* templates conforme normas básicas
* retenção de 20 anos (configurada)
* sigilo via RBAC

---

## Fase 2

* assinatura ICP-Brasil
* templates revisados por advogado
* sigilo psicológico via django-guardian + RLS
* odontograma conforme CFO
* receita digital com QR code

---

## Fase 3

* certificação SBIS NGS2 (se demanda existir)
* SNGPC para controlados
* auditoria externa de compliance
* parecer regulatório formal

---

# 23. O QUE EVITAR

Nunca permitir:

* exclusão de prontuário sem audit
* alteração silenciosa de receita emitida
* exposição de dados psicológicos sem autorização
* templates que violem normas legais
* assinatura sem rastreabilidade
* publicidade que viole código de ética

---

# 24. RELAÇÃO COM LGPD

Compliance com conselhos COMPLEMENTA LGPD, não substitui.

* CFM exige retenção 20 anos (lawful basis: obrigação legal)
* LGPD exige base legal + tratamento adequado
* sigilo profissional + sigilo de dado pessoal sensível andam juntos

Ver `docs/lgpd.md`.

---

# 25. DOCUMENTAÇÃO PARA O CLIENTE

Disponibilizar:

* lista de normas atendidas
* matriz de responsabilidades
* templates aprovados
* certificados (quando aplicáveis)
* canal para reportar problemas de compliance

---

# 26. OBJETIVO FINAL

Construir um produto que:

* respeita rigorosamente todos os conselhos relevantes
* documenta o que cobre e o que é responsabilidade do profissional
* evolui conforme normas evoluem
* serve como argumento de venda (defensabilidade)
* nunca expõe profissional a risco de fiscalização por uso do sistema
