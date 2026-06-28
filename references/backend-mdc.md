`.cursor/rules/backend.mdc` aplica regras do Cursor restritas ao código de backend, via `globs` (modo "Apply to Specific Files"):

```yaml
---
description: "Convenções de backend deste projeto."
globs: api/**/*.js
---
```

## Correção de sintaxe em relação ao guia genérico original

O exemplo original (`applyTo: "src/api/**/*.py"`) usava um campo `applyTo` que não existe no Cursor — o campo correto é `globs`, e a lista de padrões é uma string separada por vírgula, sem colchetes/aspas envolvendo cada padrão.

## Nota sobre o glob neste projeto

`src/api/**/*.py` é o exemplo genérico do guia multi-agente (voltado a um backend Python). O backend mock de `promptdown-ui-v3` é Node.js/Express em `api/`, então o glob real é `api/**/*.js`.

## Fontes

- https://cursor.com/docs/rules
