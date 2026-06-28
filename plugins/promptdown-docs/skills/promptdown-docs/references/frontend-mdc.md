`.cursor/rules/frontend.mdc` aplica regras do Cursor restritas ao código de frontend, via `globs` (modo "Apply to Specific Files"):

```yaml
---
description: "Convenções de frontend deste projeto."
globs: public/js/**/*.js
---
```

## Correção de sintaxe em relação ao guia genérico original

O exemplo original (`applyTo: "src/**/*.tsx"`) usava um campo `applyTo` e sintaxe de array entre aspas — **nenhum dos dois corresponde ao frontmatter real do Cursor**. O campo correto é `globs`, escrito como string separada por vírgula, sem colchetes nem aspas ao redor da lista (ver `cursor-rules.md` para os detalhes de parsing e pegadinhas).

## Nota sobre o glob neste projeto

`src/**/*.tsx` é o exemplo genérico do guia multi-agente (voltado a um projeto React/TSX). Como `promptdown-ui-v3` é uma SPA vanilla em JS puro, com o frontend em `public/js/`, o glob real aqui é `public/js/**/*.js` — não `src/**/*.tsx`.

## Fontes

- https://cursor.com/docs/rules
