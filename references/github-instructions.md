`.github/instructions/` contém as **instruções específicas por caminho** ("path-specific custom instructions") do GitHub Copilot — aplicadas só a requisições no contexto de arquivos que casam com um padrão glob declarado no frontmatter de cada arquivo.

## Convenção de nome de arquivo

Cada arquivo deve se chamar `NAME.instructions.md`, onde `NAME` indica o propósito da instrução — a extensão `.instructions.md` é **obrigatória** (não basta `.md`).

```
.github/instructions/
├── api.instructions.md
├── frontend.instructions.md
└── security.instructions.md
```

Subpastas são permitidas para organizar os arquivos por tema.

## Frontmatter

| Campo | Obrigatório | Descrição |
|---|---|---|
| `applyTo` | **Sim** | Padrão glob (ou lista separada por vírgula) definindo a quais arquivos a instrução se aplica |
| `excludeAgent` | Não | Exclui um agente específico (ex.: `"code-review"`) de usar essa instrução. Se omitido, tanto Copilot code review quanto Copilot coding agent (cloud agent) usam o arquivo |

```yaml
---
applyTo: "**/*.ts,**/*.tsx"
---

Use TypeScript strict mode. Prefer interfaces over type aliases for object shapes.
```

**Atenção, mesma pegadinha do Cursor**: múltiplos padrões em `applyTo` são separados por **vírgula dentro de uma única string**, não como array YAML.

## Onde o VS Code procura esses arquivos

Configurável via a setting `chat.instructionsFilesLocations` (padrão: `.github/instructions`). É possível habilitar locais adicionais, incluindo um diretório de perfil de usuário (global, ex.: `~/.copilot/instructions`, desabilitado por padrão) — ou seja, diferente de `copilot-instructions.md` (sempre por repositório), `.instructions.md` pode, opcionalmente, ter um equivalente de escopo pessoal/global dependendo da configuração do editor.

```json
"chat.instructionsFilesLocations": {
  ".github/instructions": true,
  "~/.copilot/instructions": false
}
```

Em monorepos, a setting `chat.useCustomizationsInParentRepositories` permite descobrir instruções a partir da raiz do repositório pai.

## Verificando se uma instrução foi aplicada

Se `applyTo` não estiver presente, o arquivo de instrução **não** é aplicado automaticamente. A seção "References" na resposta do chat mostra quais arquivos de instrução foram efetivamente usados.

## Fontes

- https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
- https://code.visualstudio.com/docs/copilot/customization/custom-instructions
