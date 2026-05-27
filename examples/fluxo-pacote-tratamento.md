# Fluxo de Pacote de Tratamento — Clinix

> Jornada completa de um pacote de tratamento, da venda à conclusão. Aplica-se a estética, odontologia, fisioterapia e nutrição.

---

# 1. PERSONAS ENVOLVIDAS

* paciente
* profissional clínico
* recepcionista (agendamento, cobrança)
* financeiro
* sistema (automações, cobrança recorrente)

---

# 2. CANAIS DE ENTRADA

* avaliação presencial (mais comum em estética/odonto)
* indicação de pacote por profissional após consulta
* portal do paciente (renovação self-service — Fase 2)

---

# 3. PRÉ-CONDIÇÕES

* paciente cadastrado
* avaliação inicial registrada (anamnese da especialidade)
* contraindicações verificadas (estética)
* clínica tem o pacote configurado no catálogo

---

# 4. ETAPA 1 — AVALIAÇÃO E PROPOSTA

## Passos

1. paciente comparece à avaliação
2. profissional registra anamnese (template da especialidade)
3. profissional avalia clinicamente
4. sistema sugere pacotes compatíveis (baseado em diagnóstico/objetivo)
5. profissional adiciona/remove itens
6. orçamento gerado em PDF

## Resultado

* `EstheticAssessment` ou `TreatmentPlan` criado
* orçamento em PDF disponível
* paciente recebe via portal/WhatsApp

---

# 5. ETAPA 2 — APROVAÇÃO E TERMO

## Passos

1. paciente recebe link com orçamento
2. visualiza no portal
3. lê termo de consentimento específico (procedimento)
4. assina digitalmente (assinatura simples no MVP; ICP-Brasil na Fase 2)
5. opta por forma de pagamento e parcelamento

## Resultado

* termo assinado registrado (IP, timestamp, hash, versão)
* `TreatmentPlan.status = "approved"`
* `approved_by_patient_at` preenchido

---

# 6. ETAPA 3 — VENDA E FINANCEIRO

## Passos

1. recepcionista (ou paciente self-service) confirma venda
2. sistema cria `TreatmentPackageInstance`
3. cobrança gerada (`Charge`) com parcelamento configurado
4. Mercado Pago configura cobrança recorrente (cartão) ou PIX/boleto avulso por parcela
5. paciente paga 1ª parcela imediatamente

## Validações

* limite de parcelas conforme plano da clínica
* desconto aplicável (Fase 2)
* taxa de juros conforme política

## Resultado

* `Charge` confirmado para parcela 1
* parcelas futuras agendadas
* receita reconhecida conforme regime contábil

---

# 7. ETAPA 4 — AGENDAMENTO DAS SESSÕES

## Opção A — Série completa imediata

1. sistema sugere agendar todas as N sessões em série
2. respeita `expected_interval_days` do pacote
3. paciente confirma horários
4. todas as `TreatmentPackageSession` criadas com `status=scheduled`

## Opção B — Uma a uma

1. apenas próxima sessão é agendada
2. ao concluir sessão, sistema sugere agendar a próxima
3. flexibilidade maior, exige mais coordenação

---

# 8. ETAPA 5 — EXECUÇÃO DE SESSÃO

## Passos

1. paciente faz check-in
2. profissional abre o pacote no prontuário
3. visualiza progresso (ex: 4/10)
4. (estética) revalida contraindicações rapidamente
5. captura **foto antes** (presigned PUT → R2 → Celery comprime)
6. executa procedimento
7. registra evolução em `RecordEntry` usando template (`sessao_estetica`, etc.)
8. captura **foto depois**
9. marca sessão como `done`
10. sistema incrementa `sessions_consumed`

## Validações

* contraindicações ativas bloqueiam execução
* profissional precisa ter `specialty` válida para o procedimento
* sessão fora de ordem requer justificativa

## Resultado

* `TreatmentPackageSession.executed_at` preenchido
* `ClinicalImage` antes e depois com `paired_with`
* `RecordEntry` salvo
* event bus: `package_session.executed`

---

# 9. ETAPA 6 — PÓS-SESSÃO

## Automações disparadas

* WhatsApp com orientações pós-procedimento
* lembrete da próxima sessão (D-3)
* sugestão de cuidados (protetor solar, evitar atividade física, etc.)

---

# 10. ETAPA 7 — PROGRESSO INTERMEDIÁRIO

Após cada sessão:

* paciente vê progresso no portal
* fotos antes/depois acumulam (se autorizado)
* recepção/profissional veem dashboard de pacotes ativos

---

# 11. ETAPA 8 — CONCLUSÃO

## Quando `sessions_consumed == total_sessions`

1. `TreatmentPackageInstance.status = "completed"`
2. profissional faz consulta de encerramento
3. relatório final gerado (com fotos antes/depois consolidadas)
4. WhatsApp celebratório enviado
5. sugestão de manutenção ou novo pacote (upsell)

---

# 12. ETAPA 9 — RENOVAÇÃO / UPSELL

## D+7 após conclusão

* automação: "Como você está se sentindo após o tratamento?"
* link para responder NPS
* CTA para agendar manutenção ou novo pacote

---

# 13. CENÁRIOS DE EXCEÇÃO

## Sessão perdida (no-show)

* sessão marcada como `missed`
* política configurável: consome a sessão ou não
* automação de recuperação dispara
* recepção tenta reagendar

## Pacote pausado

* paciente solicita pausa (gravidez, mudança, viagem longa)
* `status="paused"` com motivo
* parcelamento pode ser pausado também (caso a caso)
* retomada agenda restante das sessões

## Cancelamento

* `status="cancelled"`
* motivo obrigatório
* política de reembolso aplicada (proporcional ao não-consumido)
* sessões agendadas futuras canceladas
* fotos preservadas (ou anonimizadas conforme LGPD)

## Inadimplência

* atraso > 5 dias dispara cobrança preventiva
* atraso > 15 dias bloqueia próximas sessões (configurável)
* atraso > 30 dias notifica gerência

---

# 14. INTEGRAÇÃO COM PRONTUÁRIO

* cada sessão gera 1 `RecordEntry`
* timeline do paciente mostra histórico do pacote
* comparação de fotos antes/depois acessível pelo profissional

---

# 15. RELATÓRIOS

* receita por pacote (catálogo)
* taxa de conclusão por pacote
* ticket médio
* taxa de no-show de sessão
* LTV por paciente
* taxa de renovação
* tempo médio de conclusão (esperado vs real)

---

# 16. AUDITORIA

Eventos:

* `package.sold`
* `package.approved_by_patient`
* `package_session.scheduled`
* `package_session.executed`
* `package_session.missed`
* `package_session.voided`
* `package.paused`
* `package.completed`
* `package.cancelled`
* `before_after_photo.captured`

---

# 17. EXEMPLO REAL — ESTÉTICA

Cenário: **Clínica Bellare** vende pacote de drenagem de 10 sessões.

1. Cliente comparece à avaliação
2. Esteticista preenche `anamnese_estetica` + checklist
3. Sistema sugere "Drenagem 10 sessões" (R$ 1.500)
4. Cliente aprova, assina termo digital, paga em 6x no cartão
5. 10 sessões agendadas em série (semanal)
6. Sessão 1: foto antes, drenagem, foto depois, orientações via WhatsApp
7. Sessões 2–9: rotina semanal
8. Sessão 10: relatório final com fotos comparativas + NPS
9. D+7: WhatsApp sugere manutenção mensal
10. Cliente compra "Manutenção 6 meses" (renovação)

---

# 18. EXEMPLO REAL — ODONTOLOGIA

Cenário: **Tratdent** vende plano ortodôntico.

1. Avaliação registra odontograma inicial
2. Plano de tratamento criado com 24 procedimentos
3. Paciente aprova, paga R$ 4.800 em 24x
4. Sessões agendadas mensalmente
5. Cada sessão atualiza odontograma + cobra parcela
6. Após 24 meses: alta ortodôntica, fotos comparativas, manutenção semestral

---

# 19. EXEMPLO REAL — FISIOTERAPIA

Cenário: **Ortofisio** vende reabilitação pós-cirúrgica.

1. Avaliação funcional inicial (ROM, força, dor)
2. Plano "Reabilitação 20 sessões" R$ 2.000 em 4x
3. Sessões 2x/semana
4. Cada sessão registra evolução (ROM melhorando, dor diminuindo)
5. Sessão 20: alta clínica, gráfico de evolução, orientações de manutenção
6. D+30: WhatsApp pergunta como está

---

# 20. EXEMPLO REAL — NUTRIÇÃO

Cenário: **Acompanhamento 6 meses** com nutricionista.

1. Avaliação completa + antropometria + plano alimentar
2. Pacote "6 consultas mensais" R$ 1.200 em 6x
3. Cada consulta: nova antropometria, ajuste do plano, novo PDF enviado
4. Entre consultas: paciente registra peso semanal e recordatório
5. Sessão 6: relatório final + orientações de manutenção
6. Renovação: novo pacote ou consulta avulsa trimestral

---

# 21. REFERÊNCIAS

* `docs/pacotes-tratamento.md`
* `docs/estetica.md`
* `docs/odontograma.md`
* `docs/nutricao.md`
* `docs/imagens-clinicas.md`
* `docs/billing.md`
* `examples/fluxo-financeiro.md`
* `examples/fluxo-plano-alimentar.md`
