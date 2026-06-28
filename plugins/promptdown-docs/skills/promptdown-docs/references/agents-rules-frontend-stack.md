`.agents/rules/frontend-stack.md` é um exemplo concreto de Rule de workspace do Antigravity IDE, adaptado às convenções reais de `promptdown-ui-v3` (SPA vanilla em JS puro) — em vez do exemplo genérico React/Tailwind usado no tutorial oficial do Google.

```markdown
# Frontend Stack Guidelines

Aplica-se a: arquivos em `public/js/**/*.js` e `public/css/**/*.css`.

## JavaScript

- Sempre `const`/`let`, nunca `var`; sempre `===`/`!==`.
- Nunca usar `innerHTML` com conteúdo dinâmico — preferir `.textContent`
  ou `<template>` + `createElement`/`document.importNode`.
- Views (`public/js/views/*.js`) devem retornar um contrato de lifecycle
  (`{ destroy }` ou `null`) e limpar subscriptions antes de trocar de view.
- Roteamento via History API (`pushState`/`popstate`) — nunca hash routing.

## CSS

- Usar tokens/variáveis CSS (`var(--color-bg)`, `var(--spacing-*)`) em vez
  de valores hardcoded — ver @public/css/tokens.css.
- `rem` para fontes e espaçamentos, nunca `px`.

Para o componente de card especificamente, ver @public/js/components/card.js
e @public/css/components/card.css antes de propor mudanças de estrutura.
```

## Por que esse exemplo, e não o genérico do tutorial

O tutorial oficial do Google usa como exemplo uma regra para React + Tailwind ("All React components must be Functional Components using Hooks"). Esse exemplo não se aplica a `promptdown-ui-v3`, que é uma SPA vanilla sem framework — por isso este exemplo concreto reaproveita as convenções já documentadas no `CLAUDE.md` global do usuário e nos `references/` deste projeto (`app-js.md`, `card-js.md`, `tokens-css.md`), mostrando a Rule funcionando com a stack real em vez de uma stack hipotética.

## Recursos de Rule demonstrados neste exemplo

- **`@menção` relativa** (`@public/css/tokens.css`) — resolvida em relação à localização do próprio arquivo de Rule, conforme documentado em `agents-rules.md`.
- **Restrição implícita por tópico**: a linha "Aplica-se a: ..." documenta a intenção de escopo em prosa — a aplicação automática por glob de fato (equivalente ao `globs` do Cursor ou `applyTo` do Copilot) seria configurada nas Settings da IDE, já que a documentação oficial do Antigravity não confirma um campo de frontmatter `globs`/`paths` para Rules de workspace da mesma forma que Cursor/Copilot têm.

## Fontes

- https://antigravity.google/docs/rules-workflows
- https://medium.com/google-cloud/tutorial-getting-started-with-google-antigravity-b5cc74c103c2
