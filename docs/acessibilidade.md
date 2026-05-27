# Acessibilidade — Clinix

> Padrões de acessibilidade do Clinix.

---

# 1. VISÃO GERAL

Acessibilidade no Clinix não é opcional.

Razões:

* portal do paciente atende pacientes idosos, com deficiência visual, motora ou cognitiva
* painel operacional é usado horas seguidas por profissionais de saúde
* fadiga cognitiva e visual reduz produtividade
* compliance legal (Lei 13.146/2015 — LBI, Lei 10.098/2000)
* CFM exige condições adequadas de atendimento

---

# 2. OBJETIVOS

Garantir:

* WCAG 2.2 nível AA em todo o produto
* uso por pessoas com deficiência visual, motora, auditiva e cognitiva
* leitores de tela funcionam plenamente
* navegação 100% por teclado
* contraste adequado
* fontes legíveis
* fadiga visual mínima em uso contínuo

---

# 3. PADRÃO OFICIAL

WCAG 2.2 nível AA como padrão.

Aspirar a AAA em portal do paciente.

---

# 4. PRINCÍPIOS WCAG

Os 4 princípios:

* **Perceptível** — informação apresentável de múltiplas formas
* **Operável** — interface utilizável por qualquer meio
* **Compreensível** — informação e operação claras
* **Robusto** — funciona em diversas tecnologias assistivas

---

# 5. PERCEPÍVEL

## Contraste

* texto normal: mínimo 4.5:1 com fundo
* texto grande (18pt+): mínimo 3:1
* elementos de UI não-texto: mínimo 3:1
* validação automatizada no CI

---

## Alternativas a cor

Cor nunca é único veicular de informação:

* status com ícone + texto, não só cor
* gráficos com padrões além de cor
* erros com ícone + texto, não só vermelho
* sucesso com ícone + texto, não só verde

---

## Imagens

* alt-text obrigatório
* `alt=""` em imagens decorativas
* descrição longa para imagens complexas
* ícones com aria-label

---

## Áudio/Vídeo (telemedicina futura)

* legendas em vídeo
* transcrição em áudio
* descrição quando aplicável

---

# 6. OPERÁVEL

## Teclado

* todo elemento interativo acessível por teclado
* ordem de tab lógica
* foco visível (outline marcado)
* nunca trap de foco (a menos que intencional em modal)
* atalhos documentados:
  * `Cmd/Ctrl + K` — busca global
  * `?` — ajuda
  * `Esc` — fechar modal/drawer
  * `g + a` — ir para agenda
  * `g + p` — ir para pacientes

---

## Tap targets

Mínimo 44x44px (WCAG 2.5.5):

* botões
* links em mobile
* checkboxes
* itens de lista clicáveis

---

## Tempo

* nunca timeout silencioso
* aviso antes de expiração de sessão
* opção de estender
* salvamento automático em formulários longos

---

## Movimento

* respeitar `prefers-reduced-motion`
* animações opcionais
* sem flashes rápidos (risco epilético)

---

# 7. COMPREENSÍVEL

## Linguagem

* PT-BR claro
* sem jargão técnico exposto
* termos clínicos com tooltip quando necessário
* nunca códigos de erro brutos ("ERR_5XX") expostos ao paciente

---

## Previsibilidade

* navegação consistente entre telas
* mesmo elemento, mesmo lugar
* nunca abrir popup em focus
* nunca mudar contexto sem ação explícita

---

## Inputs

* labels visíveis e associados
* placeholders não substituem labels
* mensagens de erro claras e próximas do campo
* sugestões de correção quando possível
* formato esperado explicitado ("DD/MM/AAAA")

---

# 8. ROBUSTO

## HTML semântico

* `<button>` para botão, não `<div>`
* `<a>` para link
* headings hierárquicos (`h1` → `h2` → `h3`)
* landmarks (`<main>`, `<nav>`, `<aside>`)
* listas para listas

---

## ARIA

* usar ARIA quando HTML semântico não basta
* nunca redundante
* `aria-label` em ícones-botão
* `aria-live` para atualizações dinâmicas
* `aria-busy` em loadings
* `aria-expanded` em accordions/drawers

---

# 9. COMPONENTES DO DESIGN SYSTEM

Cada componente do design system deve:

* funcionar 100% por teclado
* ter foco visível
* anunciar estado para leitores de tela
* contraste validado
* documentação de acessibilidade no Storybook

Componentes base:

* Button, Input, Select, Textarea
* Modal, Drawer, Popover, Tooltip
* Table, Pagination
* Tabs, Accordion
* Toast, Alert
* Date picker, Time picker
* File upload

---

# 10. FORMULÁRIOS

Formulários clínicos são uso intenso:

* labels obrigatórios
* campos obrigatórios claramente marcados
* validação inline próxima ao campo
* mensagens de erro acessíveis (`aria-describedby`)
* salvamento automático em formulários longos (prontuário, anamnese)
* preview antes de submeter
* confirmação após submeter

---

# 11. TABELAS

Tabelas devem ter:

* cabeçalhos semânticos (`<th>`)
* `scope` em colunas/linhas
* caption descritiva
* navegação por teclado entre células
* sortable com aria-sort
* pagination acessível

---

# 12. MODAIS / DRAWERS

* foco move para o modal ao abrir
* foco trap dentro do modal
* `Esc` fecha
* botão de fechar visível e acessível
* `aria-modal="true"`
* foco retorna ao trigger ao fechar

---

# 13. TOASTS / NOTIFICAÇÕES

* `aria-live="polite"` para info
* `aria-live="assertive"` para erros críticos
* duração suficiente para leitura
* botão de fechar manual
* nunca som invasivo

---

# 14. DARK MODE

Quando implementado (Fase 1.5):

* contraste validado em ambos os modos
* respeita `prefers-color-scheme`
* toggle acessível
* sem mudança brusca (transição suave)

---

# 15. FONTES

* mínimo 14px em desktop, 16px em mobile
* sistema permite usuário aumentar (até 200% sem quebrar layout)
* fontes legíveis (Inter como padrão)
* nunca depender de fonte específica para informação

---

# 16. CORES — PALETA ACESSÍVEL

Paleta validada para contraste:

* primária + branco → AA+
* erro (vermelho) + branco → AA+
* sucesso (verde) + branco → AA+
* aviso (amarelo) + preto → AA+
* nunca cinza claro sobre branco

---

# 17. PORTAL DO PACIENTE

Mais rígido que o painel:

* aspirar a WCAG AAA
* tipografia maior (16-18px base)
* linguagem mais simples
* operação extremamente direta
* tutorial visual no primeiro acesso (opcional)
* contato humano fácil

Ver `docs/portal-paciente.md`.

---

# 18. MOBILE

Acessibilidade nativa:

* VoiceOver (iOS)
* TalkBack (Android)
* respeitar Dynamic Type (iOS) / Font Scale (Android)
* tap targets generosos
* gestos com alternativa por botão

Ver `docs/mobile.md`.

---

# 19. CONFORMIDADE LEGAL

## Lei Brasileira de Inclusão (Lei 13.146/2015)

* art. 63: acessibilidade em sites e apps obrigatória
* prazo: até 2018 para sites já operantes
* Clinix DEVE cumprir desde o lançamento

---

## eMAG (Modelo de Acessibilidade em Governo Eletrônico)

Referência adicional para boas práticas.

---

# 20. TESTES

## Automatizados

* `axe-core` no CI
* `lighthouse-ci` no build
* contraste validado por design tokens
* bloqueio de PR com violações

---

## Manuais

* navegação por teclado em fluxos críticos
* teste com leitor de tela (NVDA, JAWS, VoiceOver)
* teste com zoom 200%
* teste em modo alto contraste do SO

---

## Com usuários reais

Quando possível:

* sessões com pacientes idosos
* sessões com pessoas com deficiência visual
* feedback estruturado

---

# 21. AUDITORIA

Periódica:

* mensal: relatório axe-core
* trimestral: review manual de tela crítica
* anual: auditoria externa especializada

---

# 22. DOCUMENTAÇÃO

Para cada componente:

* requisitos de acessibilidade
* atalhos de teclado
* aria attributes usados
* compatibilidade com leitores testada

---

# 23. CULTURA

* acessibilidade entra na definition of done
* PR sem aria-label em ícone-botão é bloqueada
* design system enforce
* time treinado em básicos
* champion de acessibilidade no time

---

# 24. ROADMAP

## Fase 1 (MVP)

* WCAG AA em telas críticas
* axe-core no CI
* navegação por teclado em fluxos principais
* contraste validado
* alt-text obrigatório

---

## Fase 1.5

* dark mode com contraste validado
* aspiração AAA no portal do paciente
* atalhos de teclado documentados
* testes manuais com leitor de tela

---

## Fase 2

* auditoria externa
* testes com usuários reais
* alto contraste como tema dedicado
* lupa virtual em prontuário (para textos pequenos)

---

## Fase 4 (mobile)

* VoiceOver/TalkBack totalmente compatível
* gestos alternativos
* Dynamic Type respeitado

---

# 25. O QUE EVITAR

Nunca permitir:

* `<div onClick>` em vez de `<button>`
* placeholder como único label
* contraste baixo "por estética"
* tap target pequeno
* foco invisível
* cor como único veicular
* timeout sem aviso
* erro genérico ("Algo deu errado")
* PDF inacessível (sem texto selecionável)

---

# 26. OBJETIVO FINAL

Construir um produto que:

* qualquer pessoa consegue usar
* respeita LBI e WCAG AA
* é confortável em uso contínuo
* não causa fadiga visual nem cognitiva
* é argumento de venda (clínicas valorizam quando descobrem)
* nunca exclui ninguém
