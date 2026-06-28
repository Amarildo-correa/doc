`.github/instructions/api.instructions.md` aplica instruções do Copilot restritas ao código de API, via frontmatter `applyTo` (não `applyTo:` como campo solto — é dentro de um bloco YAML delimitado por `---`):

```yaml
---
applyTo: "api/**"
---

Use async/await consistently. Validate all request bodies before
touching the database. Never log request headers containing auth tokens.
```

## Correção em relação ao guia genérico original

O exemplo original usava `applyTo: "src/api/**"` corretamente quanto ao **nome do campo** (`applyTo` é de fato o campo certo, confirmado pela documentação oficial — diferente do Cursor, que usa `globs`, e do Antigravity, que usa `paths` em alguns contextos). O que precisa de correção é o **caminho em si**, específico deste projeto.

## Nota sobre o glob neste projeto

O backend mock de `promptdown-ui-v3` vive em `api/` (sem prefixo `src/`), então o glob real é `api/**`, não `src/api/**`.

## excludeAgent (opcional)

Se quiser que essa instrução valha só para o Copilot coding agent (cloud) e não para o Copilot code review, adicione:

```yaml
---
applyTo: "api/**"
excludeAgent: "code-review"
---
```

## Fontes

- https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
