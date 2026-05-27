# Personalização — Clinix

> Estratégia de personalização por clínica (branding, identidade visual, documentos, comunicação).

---

# 1. VISÃO GERAL

Cada clínica é uma marca própria.

O Clinix deve refletir essa identidade:

* nos documentos gerados (receitas, atestados, laudos, recibos)
* nas comunicações enviadas (WhatsApp, email, SMS)
* na experiência do paciente (portal, links públicos)
* na interface operacional (header, marca interna)

Sem nunca:

* comprometer consistência da UX
* permitir customização que quebre acessibilidade
* expor branding entre tenants

---

# 2. OBJETIVOS

Garantir:

* identidade visual própria por tenant
* documentos com aparência profissional da clínica
* comunicação que parece da clínica (não do Clinix)
* portal do paciente com a cara da clínica
* preparação para white-label (Fase 6)

---

# 3. PRINCÍPIOS

A personalização deve:

* ter padrões fortes por trás (a clínica não precisa configurar nada para ter resultado bom)
* permitir ajuste fino quando o cliente quiser
* validar contraste e acessibilidade
* respeitar limites do design system
* ser previewável antes de aplicar
* nunca quebrar o sistema

---

# 4. CATEGORIAS DE PERSONALIZAÇÃO

## Identidade

Logo, cores, fontes, marca.

---

## Dados de contato

Endereço, telefone, email, redes sociais.

---

## Documentos

Templates de receita, atestado, laudo, recibo, contrato.

---

## Comunicação

Templates de WhatsApp, email, assinaturas.

---

## Portal do paciente

URL, cores, mensagens.

---

## Operacional

Tipos de consulta, especialidades visíveis, fluxos personalizados.

---

# 5. IDENTIDADE VISUAL

## Configurável por tenant

* logo principal (PNG/SVG, fundo claro)
* logo monocromático (para documentos em preto e branco)
* logo reduzido / favicon
* cor primária
* cor secundária (opcional)
* slogan/tagline (opcional)

---

## Validações obrigatórias

* logo: máximo 2MB, resolução mínima 400x400
* cor primária: validação de contraste WCAG AA contra branco
* preview imediato em todos os contextos

---

# 6. DADOS DA CLÍNICA

Campos centralizados em `ClinicProfile`:

* razão social
* nome fantasia
* CNPJ
* endereço completo
* telefone fixo
* WhatsApp comercial
* email
* site
* horário de funcionamento
* responsável técnico
* registro do responsável técnico (CRM, CRO, CFP, CRBM, CREFITO)
* redes sociais (Instagram, Facebook, Google Maps)

---

# 7. UNIDADES MÚLTIPLAS

Quando a clínica tem mais de uma unidade:

* dados básicos da clínica (matriz) servem como default
* cada unidade pode sobrescrever:
  * endereço
  * telefone
  * email
  * responsável técnico
  * logo (variação)
* documentos gerados usam dados da unidade do atendimento

---

# 8. DOCUMENTOS PERSONALIZADOS

Todos os PDFs gerados incorporam:

* logo da clínica (cabeçalho)
* nome fantasia
* endereço e contato (rodapé)
* responsável técnico + registro
* cores do tenant em destaques sutis
* assinatura digital quando aplicável

---

## Tipos de documento personalizáveis

* receita médica
* receita controlada (modelo legal)
* atestado médico
* declaração de comparecimento
* relatório clínico
* encaminhamento
* laudo de exame
* recibo
* contrato de prestação de serviços
* termo de consentimento
* orçamento (odonto, estética)

---

# 9. TEMPLATES DE DOCUMENTO

Estratégia em três camadas:

## Camada 1 — Templates do Clinix

Templates oficiais, juridicamente revisados.

Cobrem 90% dos casos.

Atualizados centralmente conforme legislação evolui.

---

## Camada 2 — Customização da clínica

Clínica pode:

* trocar logo
* trocar cores
* adicionar texto introdutório/conclusivo
* adicionar campos extras
* reordenar seções predefinidas

Sem editar o HTML/CSS diretamente.

---

## Camada 3 — Templates customizados (Fase 2)

Para clientes Business+ que precisam de templates totalmente personalizados:

* editor avançado
* preview ao vivo
* validação obrigatória de campos legais
* aprovação interna antes de uso em produção

---

# 10. GERAÇÃO DE PDF

Pipeline:

```txt
Evento clínico (ex: prescrição emitida)
   ↓
Service monta contexto (dados clínicos + dados da clínica)
   ↓
Renderiza template HTML com branding aplicado
   ↓
WeasyPrint converte HTML → PDF
   ↓
Assinatura digital aplicada (se aplicável)
   ↓
Armazenamento no R2 (path com tenant_id)
   ↓
Notificação ao paciente (WhatsApp, email)
   ↓
Vinculação ao prontuário
```

---

# 11. CSS DOS DOCUMENTOS

Estrutura:

* CSS base do Clinix (tipografia, espaçamento, normalização)
* CSS de tema (cores e logo do tenant injetados como variáveis)
* CSS específico do tipo de documento (receita, atestado, etc.)

Nunca permitir CSS arbitrário do tenant — apenas variáveis controladas.

---

# 12. PRESCRIÇÃO ESPECIAL

Receitas controladas (categorias B, C) seguem modelo legal rígido:

* layout regulamentado
* logo não pode descaracterizar
* dados obrigatórios validados
* nunca aceitar customização que viole padrão

Clinix bloqueia alterações fora da norma.

---

# 13. COMUNICAÇÃO

## WhatsApp

Templates incorporam:

* nome da clínica (`{{clinic_name}}`)
* assinatura textual configurável
* link do portal personalizado
* tom de voz configurável (formal, amigável)

---

## Email

Templates HTML com:

* logo no topo
* cores do tenant
* dados de contato no rodapé
* assinatura HTML configurável
* link de descadastro (LGPD)

---

## SMS

Apenas texto:

* nome da clínica abreviado
* mensagem curta

---

# 14. PORTAL DO PACIENTE

Customizável:

* subdomínio: `{slug}.clinix.com.br` ou `portal.clinix.com.br/{slug}`
* logo no header
* cores do tenant
* mensagem de boas-vindas
* política de privacidade da clínica anexada
* termo de uso da clínica anexado
* contato/canal de atendimento

---

## Limites

* não muda layout estrutural
* não muda fluxos críticos (agendar, pagar, ver receita)
* mantém consistência de UX entre clínicas

---

# 15. DOMÍNIO PRÓPRIO (FASE 2)

Para planos Business+:

* clínica pode usar domínio próprio (`portal.clinicaexemplo.com.br`)
* configuração via CNAME
* SSL automático via Cloudflare
* validação técnica antes de ativar

---

# 16. WHITE-LABEL (FASE 6)

Para parceiros e franquias:

* esconder marca Clinix completamente
* logo Clinix removida
* "powered by" removido (no plano enterprise)
* templates de documento sem assinatura técnica do Clinix
* contratos próprios do parceiro

---

# 17. CONFIGURAÇÕES OPERACIONAIS

Cada clínica configura:

* especialidades atendidas
* tipos de consulta (`AppointmentType`)
* duração padrão por tipo
* cores por tipo (na agenda)
* status customizados
* alertas operacionais

---

# 18. TEMPLATES DE WORKFLOW

Clínica pode personalizar:

* mensagens de confirmação
* mensagens de lembrete
* mensagens de cobrança
* fluxos pós-consulta
* fluxos de retorno

A partir de templates pré-prontos do Clinix.

---

# 19. PREVIEW

Toda configuração de personalização exibe:

* preview ao vivo (sidebar)
* preview em diferentes contextos:
  * receita
  * atestado
  * laudo
  * email
  * WhatsApp
  * portal do paciente
* preview em diferentes dispositivos

---

# 20. APROVAÇÃO

Alterações em:

* logo
* cores
* templates de documento

Aplicam imediatamente após preview.

Alterações em:

* templates de comunicação (WhatsApp/email)

Podem exigir aprovação Meta (WhatsApp) antes de entrar em uso.

---

# 21. RESET PARA PADRÃO

Botão obrigatório:

* "Restaurar padrão do Clinix"

Disponível em qualquer ponto de customização.

Reverte para o template oficial em um clique.

---

# 22. PERMISSÕES

| Configuração | Quem pode editar |
|--------------|------------------|
| Logo e cores | admin |
| Dados da clínica | admin, gestor |
| Templates de documento | admin |
| Templates de WhatsApp | admin, gestor |
| Especialidades / tipos | admin, gestor |
| Workflows personalizados | admin |
| White-label | apenas super-admin Clinix |

---

# 23. AUDITORIA

Toda alteração de personalização gera audit log:

* o que mudou
* valor anterior / valor novo
* quem alterou
* quando
* preview gerado

Versionamento permite reverter para versão anterior.

---

# 24. LIMITES POR PLANO

Ver `docs/billing.md` para detalhes.

Sugestão de limites:

| Recurso | Starter | Professional | Business | Enterprise |
|---------|---------|--------------|----------|------------|
| Logo + cores | ✅ | ✅ | ✅ | ✅ |
| Templates customizados (camada 2) | ❌ | ✅ | ✅ | ✅ |
| Templates customizados (camada 3) | ❌ | ❌ | ✅ | ✅ |
| Domínio próprio | ❌ | ❌ | ✅ | ✅ |
| White-label | ❌ | ❌ | ❌ | ✅ |

---

# 25. ARMAZENAMENTO

Assets de personalização:

* logos no R2 (path `tenants/{tenant_id}/branding/`)
* templates HTML versionados no banco
* cache de PDFs gerados (TTL configurável)
* CDN via Cloudflare para logos públicas (portal do paciente)

---

# 26. PERFORMANCE

* logos servidas via CDN (Cloudflare)
* CSS de tema gerado uma vez por mudança (cacheado)
* PDFs grandes assíncronos (não bloqueiam request)
* preview otimizado (sem regerar PDF completo)

---

# 27. SEGURANÇA

* validação rigorosa de upload (mime, tamanho, dimensões)
* sanitização de campos de texto livre (XSS em PDFs)
* nunca aceitar CSS/HTML arbitrário do tenant
* nunca expor branding de outro tenant
* path do R2 sempre inclui `tenant_id`

---

# 28. ACESSIBILIDADE

A personalização não pode quebrar acessibilidade:

* contraste mínimo WCAG AA validado
* tamanho mínimo de fonte preservado
* alternativas visuais (não só cor) para estados
* alt-text obrigatório em logo

---

# 29. INTERNACIONALIZAÇÃO

PT-BR default.

Arquitetura preparada para futuras línguas:

* templates traduzíveis
* moeda configurável (futuro)
* fuso horário por clínica

---

# 30. INTEGRAÇÃO COM ONBOARDING

Durante setup inicial da clínica:

* wizard pede dados básicos
* upload de logo
* seleção de cor primária (paleta sugerida ou customizada)
* preview imediato dos documentos
* clínica já sai do onboarding com identidade aplicada

---

# 31. EXEMPLOS DE USO

## Clínica X cadastrada

* logo enviada
* cor primária `#1D9E75`
* endereço, CNPJ, responsável técnico preenchidos

---

## Médico emite receita

* PDF gerado automaticamente com:
  * logo no topo
  * endereço e contato no rodapé
  * CRM do responsável técnico
  * cor primária em destaques discretos
  * assinatura digital

---

## Paciente recebe WhatsApp

```
Olá Maria!

Sua consulta com Dr. João está confirmada para amanhã às 14h.

Endereço: Rua X, 100 — Sala 5
Telefone: (98) 99999-9999

Equipe Clínica Exemplo
```

---

## Paciente acessa portal

* logo Clínica Exemplo no header
* cores Clínica Exemplo aplicadas
* link de download da receita com identidade visual aplicada

---

# 32. O QUE EVITAR

Nunca permitir:

* CSS/HTML arbitrário injetado por tenant
* upload de logo sem validação
* alteração que viole modelo legal de prescrição
* customização que prejudique acessibilidade
* exposição de branding entre tenants
* documentos sem dados obrigatórios da clínica
* PDFs sem branding (mesmo na ausência de customização — usar default forte)

---

# 33. ROADMAP

## Fase 1 (MVP)

* upload de logo
* cor primária configurável
* dados da clínica
* templates oficiais com branding aplicado
* preview básico

---

## Fase 1.5

* templates de email com branding
* portal do paciente com cores
* assinatura textual em WhatsApp

---

## Fase 2

* templates customizados (camada 2)
* domínio próprio (Business+)
* multi-unidade com branding por unidade
* tons de voz configuráveis em workflows

---

## Fase 3

* templates customizados (camada 3 — editor avançado)
* preview em tempo real ao editar
* IA sugere paleta a partir do logo

---

## Fase 6

* white-label completo
* parceiros e franquias
* SSO branded

---

# 34. TEMPLATES CLÍNICOS POR ESPECIALIDADE

Personalização não se limita a branding. Cada clínica pode personalizar **templates clínicos** dentro do que a especialidade permite.

## Níveis de personalização

* **template global** (distribuído pelo Clinix) — read-only para o tenant
* **template do tenant** — criado do zero pela clínica
* **template estendido** — herda de global + customizações da clínica

## O que pode personalizar

* adicionar campos a um template global
* ocultar campos não essenciais
* renomear labels para o vocabulário interno
* trocar opções de selects (com curadoria)
* mudar ordem dos grupos
* publicar versões

## O que NÃO pode personalizar

* validação clínica obrigatória (não pode tornar campo obrigatório opcional se foi marcado obrigatório por motivo regulatório)
* fluxo de sigilo (psicologia mantém `is_private`)
* códigos de campos com semântica fixa (CID, CRM, FDI)

## Mecânica

Detalhada em `docs/templates-clinicos.md`. Resumo:

* template versionado por publicação
* entradas antigas renderizam com a versão antiga
* update da clínica não afeta histórico

## Branding em documentos clínicos

Toda saída em PDF (receita, atestado, plano alimentar, laudo) aplica:

* logo do tenant
* cor primária
* dados da clínica
* assinatura do profissional (com registro do conselho — CRM, CRO, CREFITO, CRP, CRN, CRBM)
* endereço e contatos
* multi-unidade (Fase 2): branding por unidade

---

# 35. OBJETIVO FINAL

Construir uma camada de personalização que:

* dá identidade própria a cada clínica
* preserva consistência de UX
* respeita normas legais de documentos médicos
* nunca quebra acessibilidade
* prepara o caminho para white-label
* é diferencial percebido no primeiro dia de uso
* permite que cada especialidade ajuste seus templates clínicos com segurança
