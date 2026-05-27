# Sprint 06 — LGPD Hardening + Meta-approval + Portal v2

> Segunda sprint da Fase 1.5. Foco em consolidar compliance, oficializar comunicação e enriquecer portal.

---

# 1. OBJETIVO

* fechar todas as pendências LGPD descobertas em operação real
* aprovar todos os templates WhatsApp na Meta (templates oficiais)
* portal do paciente versão 2 com funcionalidades pedidas pelo piloto
* coletar primeiro NPS estruturado (paciente + clínica)
* preparar onboarding repetível para próximas clínicas

---

# 2. DURAÇÃO ESTIMADA

3 semanas.

---

# 3. PRÉ-REQUISITOS

Sprint 05 concluída:

* zero bugs P0 abertos
* performance estabilizada
* runbook documentado

---

# 4. ESCOPO

## LGPD Hardening

* [ ] auditoria interna completa de tratamento de dados pessoais
* [ ] revisar todos os pontos onde dados sensíveis são logados (PII scrubbing)
* [ ] mapeamento de tratamento de dados (DPIA simplificada)
* [ ] painel do paciente: exercer direitos LGPD (acesso, exclusão, portabilidade)
* [ ] termo digital para fotos clínicas refinado
* [ ] termo opt-in para marketing separado e auditável
* [ ] anonimização de fotos antigas implementada (Celery scheduled)
* [ ] export completo do paciente (LGPD art. 18, V) em ZIP
* [ ] DPO de contato registrado (founder ou advogado)
* [ ] página de transparência de dados em `clinix.app/lgpd`
* [ ] consentimento por foto clínica re-validado (mecânica em produção)

---

## Templates WhatsApp aprovados na Meta

* [ ] inventário completo de templates necessários (15+ templates)
* [ ] templates redigidos no formato Meta (variáveis `{{1}}`, `{{2}}`)
* [ ] submissão de cada template no Meta Business Manager
* [ ] revisões e correções até aprovação
* [ ] versionamento de templates em código
* [ ] fallback para templates pendentes ainda
* [ ] templates por especialidade aprovados:
  * confirmação consulta
  * lembrete D-1
  * lembrete 2h
  * pós-consulta
  * orientação pós-procedimento (dermato/estética)
  * envio de plano alimentar (nutrição)
  * lembrete de sessão (pacotes)
  * cobrança preventiva
  * resultado de exame disponível
  * recuperação de faltoso

---

## Portal do paciente v2

* [ ] OTP via WhatsApp como método principal de login
* [ ] pré-anamnese dinâmica (formulário do template da especialidade)
* [ ] recordatório alimentar (nutrição) preenchível
* [ ] registro de peso semanal (nutrição)
* [ ] visualização de evolução antropométrica em gráfico
* [ ] resposta a escalas (psicologia) via portal
* [ ] visualização de fotos clínicas autorizadas
* [ ] PWA instalável (manifest + service worker)
* [ ] notificações push (FCM) — preparação para mobile
* [ ] área de "meus consentimentos" com histórico

---

## NPS estruturado

* [ ] automação envia NPS D+7 após cada consulta (paciente)
* [ ] dashboard de NPS por especialidade
* [ ] NPS interno mensal aplicado à clínica piloto
* [ ] entrevista semi-estruturada de feedback (15 min) gravada
* [ ] relatório consolidado em PDF

---

## Onboarding repetível

* [ ] checklist do wizard refinado
* [ ] vídeos tutoriais curtos por área (5 vídeos de 2 min)
* [ ] base de conhecimento inicial (10+ artigos)
* [ ] template de proposta comercial padronizado
* [ ] roteiro de demo (30 min)
* [ ] guia de migração de iClinic/Doctoralia (Fase 2 detalha)

---

# 5. ENTREGÁVEIS

Ao fim da sprint:

* todos os termos LGPD aprovados por advogado
* 15+ templates WhatsApp aprovados na Meta
* portal v2 acessado por > 70% dos pacientes ativos
* NPS médio > 8 (paciente) e > 8 (clínica)
* 5+ artigos de KB publicados
* primeira venda pós-MVP fechada

---

# 6. NÃO ESTÁ NO ESCOPO

* CRM (Sprint 07)
* multi-unidade (Sprint 07)
* convênios/TISS (Sprint 08)
* IA (Fase 3)
* mobile (Fase 4)

---

# 7. RISCOS

* Meta demora a aprovar templates
  * mitigação: enviar todos os templates juntos no início da sprint; usar genérico até aprovação
* DPO requisito surpreende
  * mitigação: contratar advogado-DPO temporário; founder atua como DPO inicial
* PWA tem bugs em iOS
  * mitigação: testar em iPhone real; aceitar limitação inicial
* portal v2 vira inchaço
  * mitigação: priorizar 2–3 features mais pedidas; resto vira backlog

---

# 8. CRITÉRIOS DE ACEITAÇÃO

Cada item:

* código no `main`
* testes passando
* code review aprovado
* validação em produção real (clínica piloto)
* sem regressão

---

# 9. PRÓXIMA SPRINT

Sprint 07 cobrirá:

* CRM com pipeline de leads
* multi-unidade (com branding por unidade)
* automações low-code primeiro passo

Ver `tasks/sprint-07.md`.
