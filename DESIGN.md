---
description: Design System do Promptdown — Single Source of Truth (SSoT) de tokens, grid, componentes e padrões visuais para TODAS as páginas. Consumido pelo Claude Code.
scope: frontend-browser
status: canonical
---

# Design System — Promptdown

**Estética:** dark minimalista. Separação de elementos por bordas de 1px. Cores sólidas,
sem sombras, sem degradês, sem fundos próprios em elementos sobrepostos.

> **Este documento é a única fonte de verdade (SSoT) para o visual de TODA a aplicação.**
> Vale para qualquer página — feed, editor, perfil, autenticação, configurações, modais,
> estados vazios, erros — e não apenas para cards e listas. Nenhuma página pode introduzir
> tokens, componentes ou padrões de layout fora dos definidos aqui. Se um novo elemento
> visual for necessário, ele `MUST` primeiro ser adicionado a este arquivo (token →
> componente → padrão), e só então usado em uma página.

> Regras de código (`MUST`/`MUST NOT` de JS, CSS vanilla, XSS-safety etc.) vivem em
> `CLAUDE.md` e `.specs/codebase/CONVENTIONS.md`. Este arquivo cobre exclusivamente
> decisões visuais: tokens, grid, componentes e verificações de design.

---

## 0. Lei Fundamental — Uma Tag, Uma Célula

Antes de qualquer outra regra, esta governa toda a construção de UI no Promptdown:

> **Todo elemento visual independente vive sozinho dentro de sua própria célula de grid
> (`.g-cell`). Nunca dois elementos visuais irmãos compartilham a mesma célula.**

Um "elemento visual independente" é qualquer tag que o olho percebe como uma unidade
distinta: um ícone, um trecho de texto, um número, um badge, um avatar, um botão, um
campo. Se você consegue apontar para duas coisas e dizer "isto" e "aquilo", são **duas
células**.

### 0.1 O que isto proíbe

| Anti-padrão                                                                | Por que é proibido                           | Correção                                                          |
| -------------------------------------------------------------------------- | -------------------------------------------- | ----------------------------------------------------------------- |
| `<div class="g-cell"><i></i><span>Texto</span></div>`                      | Ícone e texto são dois elementos numa célula | Uma `.g-cell` para o ícone, outra para o texto                    |
| `<div class="g-cell">autor · data</div>`                                   | Autor, separador e data são três elementos   | Três células (o separador vira a própria borda — ver § 0.4)       |
| `<div class="g-cell"><span>texto</span><span class="badge">3</span></div>` | Texto e badge são dois elementos             | Célula para o texto, célula para o badge                          |
| `<a class="g-cell"><i></i> Novo post</a>`                                  | Ícone e label são dois elementos             | Duas tags `<a>` com mesmo `href`, cada uma sua célula (ver § 0.5) |
| `<div class="g-cell"><i></i> 1.2k <i></i> 84</div>`                        | Quatro elementos (2 ícones + 2 números)      | Quatro células: ícone, número, ícone, número                      |

### 0.2 Wrapper agrupador NÃO é célula

Uma tag que apenas agrupa filhos para layout (ex.: o `<article>` de um card, um `<nav>`,
o `.layout`) **não recebe `.g-cell`**. Células são folhas visuais, não contêineres. Um
contêiner que precisa empilhar linhas usa `flex-direction: column` e cada linha é um
`.g-row` — ver o Padrão Coluna-de-Linhas em § 3.7.

### 0.3 Como decompor um bloco — algoritmo

1. Olhe o bloco e liste cada coisa visível e independente.
2. Cada item da lista → uma `.g-cell`.
3. Itens lado a lado → mesmo `.g-row`.
4. Itens empilhados → `.g-row` separados dentro de um contêiner `flex-direction: column`.
5. Espaço vazio que separa grupos → uma célula vazia com `flex: 1` (ver § 0.6).
6. Largura fixa de alinhamento entre linhas → célula _spacer_ (ver § 0.6).

### 0.4 O separador é a borda, não um caractere

Não insira `·`, `|`, `—` ou qualquer glifo separador entre dois elementos. A borda de
1px entre as duas células **é** o separador. Em vez de `autor · data` numa célula, use
uma célula `autor` e uma célula `data`; a linha vertical entre elas é o `border-right`
da célula da esquerda.

### 0.5 Link/botão composto = células irmãs com mesmo destino

Quando um controle clicável tem ícone + texto, ele vira **duas tags clicáveis** com o
mesmo `href`/handler, cada uma sua própria célula. A célula de ícone recebe
`aria-hidden="true"` e `tabindex="-1"` para não duplicar o alvo de teclado/leitor.

```html
<a href="/novo" class="action-icon g-cell" aria-hidden="true" tabindex="-1"><i class="ti ti-pencil-plus"></i></a> <a href="/novo" class="action-text g-cell">Novo post</a>
```

### 0.6 Células utilitárias (vazias, mas com função)

Duas células sem conteúdo são legítimas porque servem ao grid, não ao texto:

- **`.g-cell` _gap_** — `flex: 1; align-self: stretch;` Ocupa o espaço livre e empurra o
  que vem depois para a extremidade. Substitui `margin-left: auto` quando há grid.
- **`.g-cell` _spacer_** — largura fixa (ex.: `width: var(--card-avatar-col)`) usada nas
  linhas abaixo de uma linha com coluna lateral, para alinhar o conteúdo. Mantém o grid
  íntegro sem `padding-left` avulso.

Estas células seguem `.g-cell` normalmente (têm `border-right` + `border-bottom`).

---

## 1. Dependências visuais

### Fontes — Google Fonts

```html
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&family=Roboto+Mono:wght@400;500&display=swap" rel="stylesheet" />
```

| Token         | Família     | Uso                                                                    |
| ------------- | ----------- | ---------------------------------------------------------------------- |
| `--font-ui`   | Inter       | Toda a UI — labels, títulos, botões, nav, texto corrido                |
| `--font-code` | Roboto Mono | Exclusivo: `textarea` markdown, blocos de código e preview de markdown |

### Ícones — Tabler Icons

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@latest/dist/tabler-icons.min.css" />
```

Uso: `<i class="ti ti-{nome}" aria-hidden="true"></i>`. Nenhum emoji na interface.
Todo ícone decorativo recebe `aria-hidden="true"`.

---

## 2. Tokens

Tokens são a **única** origem de valores visuais. Nenhuma página pode usar cor, fonte,
medida ou tempo fora destes. Valor novo → criar token primeiro.

### 2.1 Cores

```css
:root {
    --color-bg: #181a18;
    --color-code-bg: #272a27;
    --color-border: #252725;
    --color-border-strong: #3d3a4d;

    --color-text: #c8c5d0;
    --color-heading: #e8e6ec;
    --color-muted: #a8a4b2;
    --color-subtle: #6b6c7a;

    --color-accent: #b09eff;
    --color-warning: #e8b84b;
    --color-error: #ff6b6b;
}
```

### 2.2 Tipografia

```css
:root {
    --font-ui: "Inter", sans-serif;
    --font-code: "Roboto Mono", monospace;

    --text-xs: 0.75rem; /* 12px — timestamps, contadores       */
    --text-sm: 0.875rem; /* 14px — labels, metadados, menu      */
    --text-base: 1rem; /* 16px — texto corrido                */
    --text-lg: 1.125rem; /* 18px — títulos de card              */
    --text-xl: 1.25rem; /* 20px — títulos de seção             */

    --font-normal: 400;
    --font-medium: 500;
    --font-bold: 700;

    --leading-tight: 1.3;
    --leading-base: 1.6;
}
```

### 2.3 Espaçamento

Escala em `rem`. Base: `1rem = 16px`.

```css
:root {
    --space-1: 0.25rem; /*  4px */
    --space-2: 0.5rem; /*  8px */
    --space-3: 0.75rem; /* 12px */
    --space-4: 1rem; /* 16px */
    --space-5: 1.25rem; /* 20px */
    --space-6: 1.5rem; /* 24px */
    --space-8: 2rem; /* 32px */
    --space-10: 2.5rem; /* 40px */
    --space-12: 3rem; /* 48px */
}
```

### 2.4 Layout

```css
:root {
    --sidebar-width: 16.25rem; /* 260px */
    --panel-min: 30rem; /* 480px */
    --transition-fast: 150ms ease;

    /* Larguras de coluna reutilizáveis (alinhamento de células) */
    --col-icon: 2.75rem; /* 44px — célula de ícone em nav, header, busca */
    --col-icon-sm: 2rem; /* 32px — célula de ícone de métrica            */
    --card-avatar-col: 3.5rem; /* 56px — coluna lateral de avatar no card      */
}
```

### 2.5 Tema claro (obrigatório)

Dark é o padrão. Todo token de cor `MUST` ter contraparte clara. Páginas não remapeiam
cores localmente — herdam deste bloco.

```css
@media (prefers-color-scheme: light) {
    :root {
        --color-bg: #f4f3f7;
        --color-code-bg: #eceaf0;
        --color-border: #d9d7e0;
        --color-border-strong: #b8b4c8;
        --color-text: #3a3748;
        --color-heading: #1a1826;
        --color-muted: #5a566a;
        --color-subtle: #8c88a0;
        --color-accent: #6b4fe8;
    }
}
```

---

## 3. Sistema de Grid — Borda Única

Esta é a regra estrutural mais importante do sistema. **Todo elemento visual independente
(ícone, texto, botão) vive dentro de sua própria célula de grid** (ver § 0). Células são
coladas umas às outras sem espaço.

### 3.1 Problema: bordas duplicadas

Quando dois elementos adjacentes cada um define borda no lado que se toca, o resultado
visual é uma linha de 2px — mais grossa que o padrão de 1px do sistema.

```
[ célula A  border-right: 1px ][ border-left: 1px  célula B ]
                              ↑↑
                           2px — ERRADO
```

### 3.2 Solução: separação de responsabilidade

A linha entre dois elementos pertence a **exatamente um** deles. A convenção adotada:

| Classe    | `border-left` | `border-top` | `border-right` | `border-bottom` |
| --------- | :-----------: | :----------: | :------------: | :-------------: |
| `.g-row`  |      ✅       |      ❌      |       ❌       |       ❌        |
| `.g-cell` |      ❌       |      ❌      |       ✅       |       ✅        |

```
                    .g-row
                border-left ↓
                ┌─────────────────────────────────────┐
                │ .g-cell          │ .g-cell           │
                │  border-right ───┤  border-right ────┤
                │  border-bottom   │  border-bottom     │
                └──────────────────┴───────────────────┘
                                   ↑
                        1 pixel — linha única
```

### 3.3 Regra para empilhamento vertical

Quando linhas (`.g-row`) são empilhadas em coluna, a separação entre elas já existe
como `border-bottom` das células da linha de cima. A linha de baixo **não adiciona
`border-top`** — seria um segundo pixel no mesmo lugar.

```
  ┌──────────────────────────────────┐
  │ .g-cell  border-bottom ──────────┤  ← linha de cima fecha aqui
  ├──────────────────────────────────┤  ← essa é a mesma borda
  │ .g-cell  border-bottom ──────────┤  ← linha de baixo NÃO tem border-top
  └──────────────────────────────────┘
```

### 3.4 Implementação canônica

```css
/* Container horizontal — abre o grid pela esquerda */
.g-row {
    display: flex;
    border-left: 1px solid var(--color-border);
    /* SEM border-top: a separação vertical vem do border-bottom das células acima */
}

/* Célula — fecha o espaço pela direita e por baixo */
.g-cell {
    display: flex;
    align-items: center;
    justify-content: center;
    border-right: 1px solid var(--color-border);
    border-bottom: 1px solid var(--color-border);
    background: transparent; /* MUST NOT ter cor própria — herda --color-bg */
    /* SEM border-top nem border-left */
}
```

### 3.5 Casos especiais

**Primeira linha de uma seção (encostada no viewport ou em outro container):**
Remover `border-left` do `.g-row` quando o limite esquerdo é o próprio viewport ou
a borda de um container adjacente já fornece o separador visual. Vale para qualquer
linha que encosta na sidebar/viewport (header, filtros, busca, linhas de card etc.).

```css
.sidebar__head.g-row,
.feed-head.g-row,
.card__row.g-row {
    border-left: none;
}
```

**Separadores de seção com cor de destaque:**
Não usar `border-top` no elemento seguinte — isso duplica com o `border-bottom` acima.
Em vez disso, alterar `border-color` das **células da seção que inicia** o bloco visual:

```css
/* Rodapé da sidebar — destaca a divisão sem duplicar borda */
.sidebar__foot .g-cell {
    border-color: var(--color-border-strong);
}
```

**Container externo (sidebar, painel):**
`MUST NOT` repetir `border-right` ou `border-left` — as células internas já os desenham.

```css
/* CORRETO */
.sidebar {
    width: var(--sidebar-width);
    display: flex;
    flex-direction: column;
}

/* ERRADO — duplica o border-right das células internas */
.sidebar {
    border-right: 1px solid var(--color-border);
} /* ← remover */
```

### 3.6 Células utilitárias

Conforme § 0.6. Padrões canônicos:

```css
/* Empurra o conteúdo seguinte para a extremidade direita */
.g-cell--gap {
    flex: 1;
    align-self: stretch;
}

/* Alinha o conteúdo de linhas inferiores com uma coluna lateral acima */
.g-cell--spacer {
    width: var(--card-avatar-col);
    flex-shrink: 0;
    align-self: stretch;
}
```

> Use as classes utilitárias OU classes nomeadas por contexto (`.card__gap-cell`,
> `.card__spacer`) com as mesmas regras. O importante é que sejam células reais.

### 3.7 Padrão Coluna-de-Linhas (bloco multilinha)

Quando um bloco tem várias linhas horizontais (ex.: um card com header, título, preview
e rodapé), o bloco **não** é um único `.g-row` com uma célula gigante. Ele é um contêiner
`flex-direction: column` onde **cada linha é um `.g-row`** e cada elemento de cada linha
é um `.g-cell`.

```
.bloco                       (flex-direction: column — NÃO é célula)
├── .g-row   [cell] [cell] [gap] [cell]
├── .g-row   [spacer] [cell]
├── .g-row   [spacer] [cell]
└── .g-row   [spacer] [cell] [cell] [gap] [cell] [cell]
```

A coluna lateral (ex.: avatar) aparece na primeira linha; as linhas seguintes usam uma
célula _spacer_ de mesma largura para manter o alinhamento.

### 3.8 Checklist do Grid

| Verificação                                               | Resultado esperado                                                                      |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `.g-row` tem `border-top`?                                | `MUST NOT` — separação vertical vem do `border-bottom` das células acima                |
| `.g-row` tem `border-right` ou `border-bottom`?           | `MUST NOT` — pertencem às células                                                       |
| `.g-cell` tem `border-top` ou `border-left`?              | `MUST NOT` — pertencem ao `.g-row`                                                      |
| Container externo tem `border-right` ou `border-left`?    | `MUST NOT` — as células já os desenham                                                  |
| Dois elementos irmãos compartilham um `.g-cell`?          | `MUST NOT` — cada tag visual independente é seu próprio `.g-cell` (§ 0)                 |
| Um wrapper agrupador recebeu `.g-cell`?                   | `MUST NOT` — células são folhas visuais, não contêineres (§ 0.2)                        |
| Há glifo separador (`·`, `\|`, `—`) entre células?        | `MUST NOT` — a borda é o separador (§ 0.4)                                              |
| Controle ícone+texto numa só tag/célula?                  | `MUST NOT` — duas tags clicáveis, duas células (§ 0.5)                                  |
| `margin-left: auto` usado para empurrar conteúdo?         | `SHOULD NOT` — usar célula _gap_ com `flex: 1` (§ 0.6)                                  |
| Alinhamento de coluna feito com `padding-left` avulso?    | `SHOULD NOT` — usar célula _spacer_ (§ 0.6)                                             |
| Célula tem `background-color`?                            | `MUST NOT` — herda `--color-bg` do body                                                 |
| Separador de seção usa `border-top` no elemento seguinte? | `MUST NOT` — usar `border-color: --color-border-strong` nas células do bloco que inicia |

---

## 4. Layout de Painéis

Colunas verticais side-by-side. Nenhum painel tem `background-color` próprio.

| Painel   | Largura           | Conteúdo                 |
| -------- | ----------------- | ------------------------ |
| Sidebar  | `--sidebar-width` | Logo, navegação, perfil  |
| Conteúdo | `flex: 1`         | Feed, form, visualização |
| Preview  | `--panel-min`     | Preview markdown         |

```css
.layout {
    display: flex;
    height: 100vh;
    background: var(--color-bg);
    color: var(--color-text);
    font-family: var(--font-ui);
}
```

A separação entre painéis é feita pelo `border-right` das células da última coluna de
cada painel — não por `border-right` no container do painel.

> Cada painel rola independentemente: o painel é `display: flex; flex-direction: column;
overflow: hidden;` e a área de conteúdo interna leva `flex: 1; overflow-y: auto;`.
> Cabeçalhos de painel são `flex-shrink: 0` para não encolher.

---

## 4.1 Scrollbar

> SSoT global. Todo elemento scrollável do projeto `MUST` usar exatamente estes pseudo-elementos. `MUST NOT` declarar `scrollbar-width` ou `scrollbar-color` fora do bloco `@supports` abaixo — essas propriedades sobrescrevem os pseudo-elementos webkit quando aplicadas diretamente.

```css
/* Chrome, Edge, Safari */
::-webkit-scrollbar {
    width: 4px;
}
::-webkit-scrollbar-track {
    background: transparent;
}
::-webkit-scrollbar-thumb {
    background: var(--color-border-strong);
    border-radius: 0;
}
::-webkit-scrollbar-button {
    display: none;
}
/* Firefox — só aplica quando ::-webkit-scrollbar não é suportado */
@supports not selector(::-webkit-scrollbar) {
    * {
        scrollbar-width: thin;
        scrollbar-color: var(--color-border-strong) transparent;
    }
}
```

**Padrão de container scrollável:**

```css
.scrollable {
    overflow-x: hidden;
    overflow-y: auto;
    /* Se for filho flex, obrigatório: */
    min-height: 0;
}
```

**Scroll horizontal é proibido em qualquer página.** Nenhum elemento — `.g-row`, card,
painel ou container — `MUST NOT` gerar `overflow-x` visível ou `scroll`. Quando uma
linha (`.g-row`) tem mais células do que cabem na largura disponível, o excesso `MUST`
ser ocultado e acessível via um controle (não via scroll lateral). Ver o exemplo de
`.card__row` com o componente § 4.2.

### 4.2 Linha com overflow — botão "mais" + popover

Quando um `.g-row` não cabe na largura do container (ex.: linha de rodapé do card com
muitas tags e métricas), as células excedentes do final da linha são removidas do fluxo
e substituídas por um botão `.g-cell` com ícone `ti-dots`, que abre um popover flutuante
listando as células ocultas. Pares `card__meta-icon` + `card__meta-count` `MUST` ser
ocultados/exibidos juntos — nunca o ícone sem o número correspondente.

```css
.row-more.g-cell {
    width: var(--col-icon-sm);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-sm);
    cursor: pointer;
}
.row-more.g-cell:hover {
    color: var(--color-heading);
}

/* Wrapper agrupador — NÃO é célula (§ 0.2), apenas posiciona o popover flutuante */
.row-popover {
    position: fixed;
    display: flex;
    flex-direction: column;
    background: var(--color-bg);
    border: 1px solid var(--color-border-strong);
    max-height: 16rem;
    min-width: 8rem;
    overflow-y: auto;
    z-index: 300;
}

/* Cada item ocultado reaparece como uma linha de grid real — Padrão
   Coluna-de-Linhas (§ 3.7). Mantém as .g-cell originais intactas, com
   border-right/border-bottom; não criar um wrapper que zere as bordas. */
.row-popover__item.g-row {
    border-left: none; /* a borda do .row-popover já delimita o lado esquerdo, ver § 3.5 */
    min-height: 2.75rem; /* touch target 44px — ver § 8.2 */
}
.row-popover__item .g-cell {
    justify-content: flex-start;
}
```

Cada `.row-popover__item` agrupa os elementos visuais correspondentes ao mesmo dado
oculto: uma `.g-cell` para uma tag isolada, ou duas `.g-cell` (ícone + número) para um
par de métrica — nunca um wrapper único cobrindo os dois sem distinção de célula.

Acessibilidade obrigatória: o botão usa `aria-haspopup="true"` e `aria-expanded`
sincronizado com o estado do popover; o popover fecha com `Esc`, clique fora, ou no
próprio botão (alternando ícone `ti-dots` ↔ `ti-x`); ao fechar, o foco retorna ao botão.

### 4.3 Painel Off-canvas (Mobile)

Abaixo de `48rem`, qualquer painel lateral (sidebar, filtros, preview) que não cabe ao
lado do conteúdo `MUST` virar um painel off-canvas: some do fluxo, reaparece como modal
fullscreen acionado por um botão flutuante. Use nomes contextuais (`.sidebar-toggle`,
`.sidebar--open`, `.sidebar__close`) seguindo este padrão genérico:

```css
.panel-toggle {
    display: none;
    position: fixed;
    bottom: var(--space-6);
    right: var(--space-6);
    z-index: 100;
    width: 3rem;
    height: 3rem;
    align-items: center;
    justify-content: center;
    background: var(--color-code-bg);
    border: 1px solid var(--color-border-strong);
    color: var(--color-heading);
    font-size: var(--text-lg);
}
.panel-toggle:hover {
    color: var(--color-accent);
    border-color: var(--color-accent);
}

.panel__close.g-cell {
    display: none;
    width: var(--col-icon);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-base);
}
.panel__close.g-cell:hover {
    color: var(--color-heading);
}

@media (max-width: 48rem) {
    .panel-toggle {
        display: flex;
    }
    .panel--off-canvas {
        display: none;
        position: fixed;
        inset: 0;
        width: 100vw;
        height: 100vh;
        z-index: 200;
        background: var(--color-bg);
    }
    .panel--off-canvas.is-open {
        display: flex;
    }
    .panel__close.g-cell {
        display: flex;
    }
    body.no-scroll {
        overflow: hidden;
    }
}
```

Acessibilidade obrigatória: `aria-expanded` no botão de toggle sincronizado com o
estado; fecha com `Esc`, clique no fundo do painel fora da área de navegação, ou no
botão de fechar; `body` recebe `no-scroll` enquanto aberto; foco vai para o botão de
fechar ao abrir e retorna ao toggle ao fechar.

---

## 5. Componentes

Todo componente abaixo segue a Lei Fundamental (§ 0). Onde o componente é composto por
mais de um elemento visual, a estrutura de células é parte da especificação.

### 5.1 Avatar

Quadrado. Letra inicial do username. Sem `border-radius`. Quando dentro de um grid, o
avatar mora em sua própria célula (`.g-cell`), nunca junto de texto.

```css
.avatar {
    width: 1.75rem;
    height: 1.75rem;
    background: var(--color-border-strong);
    color: var(--color-accent);
    font-size: var(--text-xs);
    font-weight: var(--font-bold);
    font-family: var(--font-ui);
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
}
```

### 5.2 Button

Dois estados: primário e ghost. `MUST NOT` usar `box-shadow` nem gradientes.

`.btn` é o componente isolado (fora de grid). **Dentro de um `.g-row`**, um botão com
ícone + texto vira duas células irmãs (§ 0.5); não use `.btn` com ícone embutido dentro
de uma célula.

```css
.btn {
    display: inline-flex;
    align-items: center;
    gap: var(--space-2);
    padding: var(--space-2) var(--space-5);
    font-family: var(--font-ui);
    font-size: var(--text-sm);
    font-weight: var(--font-medium);
    cursor: pointer;
    transition: opacity var(--transition-fast);
    background: transparent;
    border: 1px solid var(--color-border);
    color: var(--color-muted);
}
.btn--primary {
    border-color: var(--color-accent);
    color: var(--color-accent);
}
.btn--ghost {
    border-color: var(--color-border-strong);
}
.btn:hover {
    border-color: var(--color-accent);
    color: var(--color-accent);
}
.btn:disabled {
    cursor: not-allowed;
    opacity: 0.4;
}
```

### 5.3 Input — Text

`background: transparent`. `border` apenas para delimitar a célula de grid. O `<input>`
vive dentro de uma `.g-cell`; o ícone do campo (lupa etc.) é uma célula separada.

```css
.input {
    width: 100%;
    background: transparent;
    border: none;
    outline: none;
    font-family: var(--font-ui);
    font-size: var(--text-base);
    color: var(--color-text);
    line-height: var(--leading-base);
}
.input::placeholder {
    color: var(--color-subtle);
}
```

### 5.4 Textarea — Markdown

`MUST` usar `--font-code`. `background: transparent`. `border: none`.

```css
.textarea {
    width: 100%;
    background: transparent;
    border: none;
    outline: none;
    resize: none;
    font-family: var(--font-code);
    font-size: var(--text-base);
    color: var(--color-text);
    line-height: var(--leading-base);
    min-height: 12.5rem;
}
.textarea::placeholder {
    color: var(--color-subtle);
}
```

### 5.5 Nav Item

`.g-row` com células independentes para ícone, texto e badge. Estado ativo usa
`--color-accent`. Ícone, texto e badge são **três células distintas** — nunca o badge
dentro da célula de texto.

```html
<a href="#" class="nav-item g-row nav-item--active" aria-current="page">
    <div class="nav-item__icon g-cell"><i class="ti ti-bookmark" aria-hidden="true"></i></div>
    <div class="nav-item__body g-cell"><span class="nav-item__text">Salvos</span></div>
    <div class="nav-item__badge g-cell">3</div>
</a>
```

```css
.nav-item.g-row {
    border-left: none;
    cursor: pointer;
}

.nav-item__icon.g-cell {
    width: var(--col-icon);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-base);
}
.nav-item__body.g-cell {
    flex: 1;
    justify-content: flex-start;
    padding: var(--space-3) var(--space-4);
}
.nav-item__text {
    font-size: var(--text-sm);
    font-weight: var(--font-medium);
    color: var(--color-muted);
    transition: color var(--transition-fast);
}

.nav-item__badge.g-cell {
    flex-shrink: 0;
    padding: 0 var(--space-4);
    font-family: var(--font-ui);
    font-size: var(--text-xs);
    font-weight: var(--font-medium);
    color: var(--color-accent);
}

/* Estados */
.nav-item.g-row:hover .g-cell {
    border-color: var(--color-border-strong);
}
.nav-item.g-row:hover .nav-item__text {
    color: var(--color-heading);
}
.nav-item--active .nav-item__icon {
    color: var(--color-accent);
}
.nav-item--active .nav-item__text {
    color: var(--color-accent);
}
```

### 5.6 Badge

Quando dentro de um grid, o badge é o **único** conteúdo de sua célula (ver § 5.5).

```css
.badge {
    border: 1px solid var(--color-border);
    display: inline-flex;
    align-items: center;
    padding: var(--space-1) var(--space-2);
    font-family: var(--font-ui);
    font-size: var(--text-xs);
    font-weight: var(--font-medium);
    color: var(--color-accent);
}
```

### 5.7 Card — Padrão Coluna-de-Linhas

`MUST NOT` ter `background-color`. Separação visual feita pelo grid. O card é o exemplo
canônico do Padrão Coluna-de-Linhas (§ 3.7): o `<article>` é o contêiner em coluna (sem
`.g-cell`), e cada linha (header, título, preview, rodapé) é um `.g-row` com células
atômicas.

```
<article class="card">                    (flex-direction: column)
├── .card__row g-row   [avatar] [autor] [gap] [data]
├── .card__row g-row   [spacer] [título]
├── .card__row g-row   [spacer] [preview]
└── .card__row g-row   [spacer] [tag] [tag] [gap] [icon] [n] [icon] [n]
```

Pontos obrigatórios:

- **Header:** avatar, autor e data são três células; entre autor e data há uma célula
  _gap_ (`flex: 1`). Sem glifo `·` (§ 0.4).
- **Linhas inferiores:** primeira célula é um _spacer_ de largura `--card-avatar-col`
  para alinhar com o texto do header (§ 0.6).
- **Rodapé:** cada tag é uma célula; cada ícone de métrica é uma célula; cada número é
  uma célula; entre tags e métricas há uma célula _gap_.

```css
.card {
    display: flex;
    flex-direction: column;
    cursor: pointer;
}
.card__row.g-row {
    border-left: none;
}
.card:hover .g-cell {
    border-color: var(--color-border-strong);
}

.card__avatar-col.g-cell {
    width: var(--card-avatar-col);
    flex-shrink: 0;
    align-items: flex-start;
}
.card__spacer.g-cell {
    width: var(--card-avatar-col);
    flex-shrink: 0;
    align-self: stretch;
}
.card__gap-cell.g-cell,
.card__meta-gap.g-cell {
    flex: 1;
    align-self: stretch;
}

.card__author-cell {
    font-size: var(--text-sm);
    color: var(--color-muted);
    font-weight: var(--font-medium);
}
.card__time-cell {
    font-size: var(--text-xs);
    color: var(--color-subtle);
}
.card__title {
    font-size: var(--text-lg);
    color: var(--color-heading);
    font-weight: var(--font-bold);
    line-height: var(--leading-tight);
}
.card__preview {
    font-size: var(--text-base);
    color: var(--color-text);
    font-family: var(--font-code);
    line-height: var(--leading-base);
}
.card__tag {
    font-size: var(--text-xs);
    color: var(--color-accent);
    font-weight: var(--font-medium);
}
.card__meta-icon.g-cell {
    width: var(--col-icon-sm);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-xs);
}
.card__meta-count.g-cell {
    flex-shrink: 0;
    font-size: var(--text-xs);
    color: var(--color-subtle);
    font-family: var(--font-ui);
}
```

### 5.8 Cabeçalho de Painel (Page Header)

Toda página tem um cabeçalho: ícone da seção, título e (opcional) ação. Os três são
células distintas. A ação com ícone + texto usa o padrão de link composto (§ 0.5).

```html
<div class="page-head g-row">
    <div class="page-head__icon g-cell" aria-hidden="true"><i class="ti ti-layout-list"></i></div>
    <div class="page-head__title g-cell"><h1>Feed</h1></div>
    <a href="#" class="page-head__action-icon g-cell" aria-hidden="true" tabindex="-1"><i class="ti ti-pencil-plus"></i></a>
    <a href="#" class="page-head__action-text g-cell">Novo post</a>
</div>
```

```css
.page-head.g-row {
    border-left: none;
    flex-shrink: 0;
}
.page-head__icon.g-cell {
    width: var(--col-icon);
    flex-shrink: 0;
    color: var(--color-muted);
    font-size: var(--text-xl);
}
.page-head__title.g-cell {
    flex: 1;
    justify-content: flex-start;
    padding: var(--space-4);
}
.page-head__title h1 {
    font-size: var(--text-xl);
    font-weight: var(--font-bold);
    color: var(--color-heading);
    line-height: var(--leading-tight);
}

.page-head__action-icon.g-cell,
.page-head__action-text.g-cell {
    flex-shrink: 0;
    color: var(--color-accent);
    border-right-color: var(--color-accent);
    cursor: pointer;
    font-family: var(--font-ui);
    font-size: var(--text-sm);
    font-weight: var(--font-medium);
    transition: color var(--transition-fast);
}
.page-head__action-icon.g-cell {
    width: var(--col-icon);
    font-size: var(--text-base);
}
.page-head__action-text.g-cell {
    padding: var(--space-3) var(--space-5);
}
.page-head__action-icon.g-cell:hover,
.page-head__action-text.g-cell:hover {
    color: var(--color-heading);
}
```

### 5.9 Tabs / Filtros

Faixa horizontal de filtros. Cada filtro com ícone + label vira **duas células**: ícone
e label. O espaço à direita é uma célula _gap_.

```css
.filters.g-row {
    border-left: none;
    flex-shrink: 0;
}
.filter-icon.g-cell {
    width: var(--col-icon-sm);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-sm);
}
.filter.g-cell {
    flex-shrink: 0;
    padding: var(--space-3) var(--space-4);
    font-size: var(--text-sm);
    font-weight: var(--font-medium);
    color: var(--color-muted);
    cursor: pointer;
}
.filter-sep.g-cell {
    flex: 1;
    align-self: stretch;
}

.filter-icon--active,
.filter--active {
    color: var(--color-accent);
    border-bottom-color: var(--color-accent);
}
.filter:hover,
.filter-icon:hover {
    color: var(--color-heading);
}
```

### 5.10 Campo de Busca

Ícone (lupa) em uma célula, `<input>` em outra.

```css
.search.g-row {
    border-left: none;
    flex-shrink: 0;
}
.search__icon.g-cell {
    width: var(--col-icon);
    flex-shrink: 0;
    color: var(--color-subtle);
    font-size: var(--text-base);
}
.search__input.g-cell {
    flex: 1;
    justify-content: flex-start;
    padding: var(--space-3) var(--space-4);
}
```

### 5.11 Label / Counter

```css
.field-label {
    font-size: var(--text-sm);
    color: var(--color-muted);
    font-family: var(--font-ui);
}
.field-counter {
    font-size: var(--text-xs);
    color: var(--color-subtle);
    font-family: var(--font-ui);
}
```

### 5.12 Estado Vazio / Mensagem Inline

Para listas vazias, erros e mensagens. Centralizado, em célula única que ocupa a linha.
Ícone, mensagem e ação (se houver) ficam empilhados — se houver mais de um elemento
visual em linha, aplicam-se as regras de célula normalmente.

```css
.empty.g-row {
    border-left: none;
}
.empty__cell.g-cell {
    flex: 1;
    flex-direction: column;
    gap: var(--space-4);
    padding: var(--space-12) var(--space-6);
    color: var(--color-subtle);
    font-size: var(--text-sm);
}
.empty__icon {
    font-size: 2rem;
    color: var(--color-border-strong);
}
.empty--error .empty__icon {
    color: var(--color-error);
}
```

---

## 6. Checklist de Design — Verificação Geral

Executar antes de qualquer PR que toque em CSS ou HTML, **em qualquer página**.

| Verificação                                                | Resultado esperado                                                                                     |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Dois elementos visuais irmãos no mesmo `.g-cell`?          | `MUST NOT` — Lei Fundamental, cada tag é sua própria célula (§ 0)                                      |
| Wrapper agrupador com `.g-cell`?                           | `MUST NOT` — células são folhas, não contêineres (§ 0.2)                                               |
| Glifo separador (`·`, `\|`, `—`) entre células?            | `MUST NOT` — a borda é o separador (§ 0.4)                                                             |
| Controle ícone+texto numa só célula?                       | `MUST NOT` — duas tags clicáveis, duas células (§ 0.5)                                                 |
| `margin-left:auto` em vez de célula _gap_?                 | `SHOULD NOT` — usar `.g-cell` com `flex:1` (§ 0.6)                                                     |
| Bloco multilinha como um `.g-row` gigante?                 | `MUST NOT` — usar Padrão Coluna-de-Linhas (§ 3.7)                                                      |
| Componente sobreposto tem `background-color`?              | `MUST NOT` — herda `--color-bg` do body                                                                |
| `input` ou `textarea` tem `background`?                    | `MUST NOT` — transparente; delimitação vem do grid                                                     |
| `textarea`/preview/bloco de código usa `--font-ui`?        | `MUST NOT` — `MUST` usar `--font-code` (Roboto Mono)                                                   |
| UI comum (labels, botões, nav) usa `--font-code`?          | `MUST NOT` — `MUST` usar `--font-ui` (Inter)                                                           |
| Medida de tipografia ou espaçamento em `px`?               | `MUST NOT` — `rem` obrigatório; `px` só em `border: 1px` e `outline-offset`                            |
| Usa `box-shadow`, `text-shadow` ou qualquer gradiente?     | `MUST NOT` — apenas cores sólidas dos tokens                                                           |
| Usa `border-radius`?                                       | `MUST NOT` — cantos sempre retos. Nenhuma exceção pontual                                              |
| Usa emoji na interface?                                    | `MUST NOT` — apenas ícones Tabler                                                                      |
| Valor de cor hardcoded fora dos tokens?                    | `MUST NOT` — `MUST` usar `var(--color-*)`                                                              |
| Novo valor visual sem token correspondente?                | `MUST` criar token em `:root` primeiro; depois referenciar                                             |
| Novo componente/padrão sem entrada neste arquivo?          | `MUST NOT` — este doc é a SSoT; documentar aqui antes de usar                                          |
| Unidade diferente de `rem` em fonte ou espaçamento?        | `MUST NOT`                                                                                             |
| Custom property numérica/cor sem `@property`?              | `SHOULD` tipar com `@property` para fallback robusto                                                   |
| Transição ou animação sem `prefers-reduced-motion`?        | `MUST NOT` — `MUST` respeitar `@media (prefers-reduced-motion: reduce)`                                |
| Tokens de cor sem mapeamento light theme?                  | `MUST NOT` — dark é o padrão; light `MUST` remapear via `@media (prefers-color-scheme: light)` (§ 2.5) |
| `.g-row` tem `border-top`?                                 | `MUST NOT` — ver § 3                                                                                   |
| Container externo repete borda já desenhada pelas células? | `MUST NOT` — ver § 3.5                                                                                 |
| Elemento gera scrollbar horizontal (`overflow-x`)?         | `MUST NOT` — nenhuma página rola na horizontal; conteúdo que não cabe `MUST` ser ocultado/agrupado (ver § 4.1) |
| Painel lateral sem versão off-canvas abaixo de `48rem`?    | `MUST NOT` — ver § 4.3                                                                                  |

---

## 7. Como adicionar algo novo (fluxo SSoT)

Qualquer página nova ou alteração visual segue este fluxo, nesta ordem:

1. **Precisa de um valor novo (cor, medida, tempo)?** Adicione um token em § 2 primeiro.
   Inclua a contraparte de tema claro (§ 2.5). Só então referencie via `var()`.
2. **Precisa de um componente novo?** Documente-o em § 5 com: estrutura de células,
   CSS, estados e (se interativo) acessibilidade. Só então use na página.
3. **Precisa de um arranjo de layout novo?** Verifique se é um caso do grid (§ 3) ou do
   Padrão Coluna-de-Linhas (§ 3.7). Se for genuinamente novo, descreva-o em § 3 antes de
   usar.
4. **Construiu a página?** Rode o Checklist (§ 6) inteiro antes do PR.

Se algo na página não puder ser expresso pelos tokens, componentes e padrões deste
arquivo, **o arquivo está incompleto** — atualize-o aqui primeiro. A página nunca é a
fonte de verdade; este documento é.

---

## 8. Acessibilidade

Esta seção é SSoT para todas as regras de acessibilidade (WCAG 2.1 AA) da aplicação.
Qualquer novo componente ou padrão `MUST` satisfazer os critérios abaixo antes de uso.

### 8.1 Contraste de Cores — Tokens Aprovados

#### Dark theme
| Token | Hex | vs `--color-bg` | Uso permitido |
|---|---|---|---|
| `--color-heading` | `#e8e6ec` | 14.14:1 | texto, ícones funcionais |
| `--color-text` | `#c8c5d0` | 10.30:1 | texto corrido |
| `--color-accent` | `#b09eff` | 7.65:1 | texto interativo, badges |
| `--color-muted` | `#a8a4b2` | 7.18:1 | texto secundário, labels |
| `--color-error` | `#ff6b6b` | 6.31:1 | mensagens de erro |
| `--color-warning` | `#e8b84b` | 9.49:1 | avisos |
| `--color-subtle` | `#6b6c7a` | 3.38:1 | **apenas ícones decorativos e bordas** |

#### Light theme
| Token | Hex | vs `--color-bg` | Uso permitido |
|---|---|---|---|
| `--color-heading` | `#1a1826` | 15.81:1 | texto, ícones funcionais |
| `--color-text` | `#3a3748` | 10.45:1 | texto corrido |
| `--color-muted` | `#5a566a` | 6.39:1 | texto secundário, labels |
| `--color-accent` | `#6b4fe8` | 4.86:1 | texto interativo, badges |
| `--color-subtle` | `#8c88a0` | 3.09:1 | **apenas ícones decorativos e bordas** |

> `--color-subtle` `MUST NOT` ser usado em texto funcional (timestamps, counts, labels,
> handles). Contraste de 3.38:1 dark / 3.09:1 light é insuficiente para WCAG 1.4.3 AA
> (mínimo 4.5:1 para texto normal).

### 8.2 Touch Targets

- Todo elemento interativo `MUST` ter área de toque ≥ 44 × 44 CSS pixels (WCAG 2.5.5).
- Linha de container (`g-row`) com elemento interativo `MUST` ter `min-height: 2.75rem`.
- O `.sidebar__head.g-row` `MUST` declarar `min-height: 2.75rem` para garantir o target
  do botão de fechar no mobile.
- Elementos puramente decorativos (ícones `aria-hidden`, divisores) são isentos.

### 8.3 Indicador de Foco

- Todo elemento interativo `MUST` ter `:focus-visible` visível — nunca `outline: none`
  sem substituto.
- Padrão canônico para elementos dentro do grid:
```css
:focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: -2px;
}
```
- Para `.input` e elementos de formulário, usar `outline-offset: 2px` (fora da borda).
- `MUST NOT` remover `outline` de `<input>`, `<button>`, `<a>` ou qualquer elemento
  com `tabindex >= 0`.

### 8.4 Tipografia para Leitura Prolongada

| Uso | Token mínimo | Line-height mínimo | Justificativa |
|---|---|---|---|
| Texto corrido / preview | `--text-base` (16px) | `--leading-base` (1.6) | leitura confortável em mobile |
| Titles de card | `--text-lg` (18px) | `--leading-tight` (1.3) | aceitável para headings |
| Labels / metadados | `--text-sm` (14px) | herdado (1.6) | ≥ 14px garante legibilidade |
| Timestamps / counts | `--text-xs` (12px) | herdado (1.6) | limite mínimo; `--color-muted` obrigatório |

- `--font-code` (Roboto Mono) `MUST NOT` ser usado em prosa corrida: apenas em blocos de
  código, `<textarea>` markdown e preview de código. Texto de post (título, excerpt)
  `MUST` usar `--font-ui`.

### 8.5 Semântica e ARIA

- `role="banner"` `MUST NOT` ser usado em cabeçalhos de seção — reservado ao cabeçalho
  principal do documento (equivalente ao `<header>` de `<body>`).
- Badges numéricos `MUST` ter `aria-label` explicando o contexto
  (`aria-label="3 itens salvos"`, não apenas `"3"`).
- Section labels da sidebar `MUST` ter `role="heading" aria-level="3"` ou estar dentro
  de `<section aria-labelledby>` equivalente.
- Ícones decorativos `MUST` ter `aria-hidden="true"`. Nunca texto alternativo em ícones
  puramente visuais.
- O padrão `role="tablist"` + `role="tab"` `MUST` incluir `aria-controls` apontando para
  o `tabpanel` correspondente, e navegação por arrow keys ←→.

### 8.6 Modal e Focus Management

- Toda sidebar ou painel abrindo como modal fullscreen `MUST` implementar focus trap:
  Tab e Shift+Tab ciclam dentro do modal; foco não escapa ao conteúdo por baixo.
- Ao abrir: foco vai para o primeiro elemento focusable dentro do modal (ex.: botão fechar).
- Ao fechar: foco retorna ao elemento que acionou o modal.
- `body.no-scroll` `MUST` ser aplicado enquanto modal estiver aberto.

### 8.7 Navegação por Teclado — Skip Link

Toda página `MUST` ter um link "pular para conteúdo" como primeiro elemento do `<body>`,
visível apenas no foco:

```html
<a href="#main" class="skip-link">Pular para o conteúdo principal</a>
```

```css
.skip-link {
    position: absolute;
    left: -9999px;
}
.skip-link:focus-visible {
    left: var(--space-4);
    top: var(--space-2);
    z-index: 1000;
    padding: var(--space-2) var(--space-4);
    background: var(--color-accent);
    color: var(--color-bg);
    outline: 2px solid var(--color-heading);
}
```
