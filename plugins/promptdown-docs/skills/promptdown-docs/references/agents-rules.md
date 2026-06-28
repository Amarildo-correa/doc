`.agents/rules/` contém as **Rules** do Antigravity IDE — arquivos Markdown com restrições e diretrizes que o agente segue, fornecendo contexto persistente e reutilizável no nível do prompt (diferente de Workflows, que guiam uma sequência de passos no nível da trajetória da tarefa).

## Localização por escopo

| Escopo | Localização |
|---|---|
| Workspace | `.agents/rules/*.md` (com fallback retroativo para `.agent/rules/`) |
| Global (todos os workspaces) | Gerenciado via Settings da IDE — não documentado como pasta de arquivos simples |

## Aplicação por padrão de arquivo (glob)

Rules podem ser restritas a arquivos que casem com um padrão — quando configurado, "a regra é aplicada a todos os arquivos que correspondem ao padrão".

## Sintaxe de `@menções`

Rules suportam `@filename` para referenciar outros arquivos:

- Path relativo → relativo à localização do próprio arquivo de rule.
- Path absoluto → resolvido como caminho absoluto; se o arquivo não existir nesse caminho, o Antigravity tenta resolver como `workspace/path/to/file.md`.

```markdown
@/path/to/style-guide.md
@../shared/conventions.md
```

## Exemplo prático (do tutorial oficial)

```
Frontend Stack Guidelines

Functional Components: All React components must be Functional
Components using Hooks. Class components are forbidden.

Styling: Use Tailwind CSS utility classes. Do not use inline
styles or separate CSS files.

Naming: Use PascalCase for component filenames (e.g., UserProfile.tsx).
```

## Fontes

- https://antigravity.google/docs/rules-workflows
- https://medium.com/google-cloud/tutorial-getting-started-with-google-antigravity-b5cc74c103c2
