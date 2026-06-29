`.claude/rules/code-style.md` define convenções de escrita de código para
toda sessão do Claude Code. Gerado de `plugins/promptdown-helpers/rules/code-style.md`.

## Conteúdo da rule

```markdown
# Code Style — promptdown-ui-v3

## JavaScript

- Sempre `const`/`let`, nunca `var`.
- Sempre `===`/`!==`, nunca `==`/`!=`.
- ES Modules (`import`/`export`), nunca `require`/`module.exports`.
- Nunca `innerHTML` com conteúdo dinâmico — usar `.textContent` ou
  `<template>` + `createElement`/`document.importNode`.
- Roteamento via History API (`pushState`/`popstate`), nunca hash routing.

## CSS

- Usar variáveis CSS (`var(--color-bg)`, `var(--spacing-*)`) — nunca valores hardcoded.
- `rem` para fontes e espaçamentos, nunca `px`.

## Geral

- Comentar apenas o "porquê", nunca o "o quê".
- JSDoc opcional; sem TypeScript por padrão neste projeto.
```

## Fonte canônica

`plugins/promptdown-helpers/rules/code-style.md` — edite lá, nunca aqui.

## Fontes

- `claude-rules.md`
- `promptdown-ui-v3.md`
