# Acessibilidade

Auditoria WCAG 2.1 AA feita em 17/07/2026, com foco em leitor de tela e teclado
(motivação: clientes cegos ou com baixa visão devem conseguir usar o site inteiro).
As mudanças abaixo são mantidas no `index.html` do projeto. **Nenhuma altera o visual.**

## O que foi feito

### Semântica e anúncios dinâmicos — etapa 2

- **FAQ fechado saiu da árvore acessível**: cada painel acompanha o botão com
  `aria-hidden` e `inert`, enquanto `aria-expanded` continua indicando o estado.
  A animação visual foi preservada e, sem JavaScript, o `<noscript>` mantém todas
  as respostas visíveis e disponíveis.
- **Perguntas do FAQ viraram headings `h3`**: o botão continua sendo o único
  controle dentro do heading, facilitando o índice de títulos do leitor de tela
  sem alterar tipografia, espaçamento ou animação.
- **CTAs dos planos ficaram distinguíveis**: o texto visível continua "Quero meu
  site", mas os nomes acessíveis incluem Essencial, Profissional ou Autoridade e
  preservam o texto visível dentro do nome (WCAG 2.4.4 / 2.5.3).
- **Resultado do quiz ganhou um status curto e atômico**: a região visual deixou
  de ser anunciada inteira a cada clique. Um `role="status"` invisível informa
  apenas a quantidade selecionada e o plano recomendado (WCAG 4.1.3).
- **Tooltips têm descrição estável**: cada pill recebe um `aria-describedby`
  próprio, criado a partir do `data-tip`. O balão compartilhado ficou apenas
  visual, evitando depender do instante em que o foco adiciona a descrição.

### Navegação por teclado e leitor de tela

- **Menu mobile fechado não recebe mais foco** (WCAG 2.4.3 / 2.4.7).
  O painel usava só `opacity:0` + `pointer-events:none`, que não bloqueiam Tab nem
  leitor de tela — os 6 links continuavam alcançáveis, invisíveis. Agora o estado
  fechado tem `visibility:hidden` no CSS e `inert` alternado no JS (`setMenu`).
- **Barra fixa de WhatsApp (mobile) idem**: `visibility:hidden` quando fora da tela.
- **H1 estável durante o rotor** (WCAG 1.3.1). O texto animado
  (`#rotor`) é `aria-hidden="true"` e um `<span class="sr-only">você</span>` fixo
  garante que o heading sempre é lido como "A página que faz o cliente escolher você",
  em qualquer momento da animação. Utilitária `.sr-only` criada no CSS.
- **Nomes dos planos viraram headings**: Essencial, Profissional e Autoridade são
  `<h3 class="name">` (antes `<div>`). Navegar por headings é o principal jeito de
  quem usa leitor de tela escanear a página — os planos agora aparecem nesse índice.
  O estilo é 100% por classe, então nada mudou visualmente.
- **Footer**: "Navegar" e "Contato" viraram `<h3>` (eram `<h4>`, pulando nível).

### Nomes acessíveis

- **Todos os links de `wa.me` anunciam "(abre conversa no WhatsApp)"** via
  `aria-label` montado no init do JS. O botão do quiz (`#qrBtn`) muda de texto
  dinamicamente, então o label dele é atualizado dentro do `updateQuiz` — se mexer
  no texto do botão, atualizar o label junto (WCAG 2.5.3, label-in-name).
- Os CTAs com animação de letras (`.btn-roll`) já tinham `aria-label` estável para
  o leitor não ler os spans fatiados — mantido.

### Robustez sem JS

- Os glifos decorativos (✓ e ✕ dos cards de comparação, + do FAQ, caixa do quiz)
  têm `aria-hidden="true"` direto no markup. Antes era aplicado via JS — se o
  script falhasse, o leitor de tela lia "✕ Um site ultrapassado…" em voz alta.
  O bloco de JS redundante foi removido.

## Decisões conscientes (não mexer sem o Alessandro pedir)

- **Contrastes abaixo do AA formal mantidos por decisão de design (17/07/2026)**:
  CTA do header e badge "Mais escolhido" (branco/laranja 3.28:1), subtítulo (2.31:1)
  e linha de preço (1.98:1) do CTA final laranja, pill "Já vem com:" (4.45:1).
  Alessandro avaliou um a um e julgou legíveis; para o CTA final ele quer pensar a
  própria solução. Isso afeta baixa visão, não cegueira total — leitor de tela não
  depende de contraste.
- Tooltip da pill some antes de o mouse alcançá-la (WCAG 1.4.13) — baixo impacto,
  pois clique e foco também abrem; mantido.

## O que a página já tinha de bom (manter em qualquer edição)

Skip link, `:focus-visible` com cor por seção, `prefers-reduced-motion` em todas as
animações, FAQ com `aria-expanded`/`aria-controls`, quiz com `role="checkbox"` +
`aria-checked` + status em `aria-live="polite"`, rotor que roda um ciclo e para
(WCAG 2.2.2), `alt` descritivo nas imagens, hambúrguer 44×44px, fallback `<noscript>`
do FAQ.

## Checklist pra futuras mudanças

1. Elemento escondido por opacity/transform? Precisa também de `visibility:hidden`
   ou `inert` — senão vira foco fantasma.
2. Texto que muda sozinho dentro de heading? `aria-hidden` no animado + cópia fixa
   em `.sr-only`.
3. Ícone decorativo novo? `aria-hidden="true"` no markup, não via JS.
4. Link novo de WhatsApp? O loop do init já pega (`a[href*="wa.me"]`) — só não
   sobrescrever o `aria-label` depois.
5. Card/bloco novo com título? Usar heading real (`h2`/`h3`) estilizado por classe.
6. Teste rápido: Tab do topo ao fim (nada invisível recebe foco?) e VoiceOver
   (Cmd+F5) navegando por headings (VO+Cmd+H).
