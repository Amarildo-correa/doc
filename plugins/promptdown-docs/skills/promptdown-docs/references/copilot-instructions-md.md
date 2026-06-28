`.github/copilot-instructions.md` contém as **instruções de repositório de escopo amplo** ("repository-wide") do GitHub Copilot — aplicadas a todas as requisições feitas no contexto deste repositório, em qualquer feature do Copilot que suporte customização.

## Os três tipos de custom instructions do Copilot

| Tipo | Onde fica | Aplica-se a |
|---|---|---|
| **Repository-wide** | `.github/copilot-instructions.md` | Todas as requisições no contexto do repositório |
| **Path-specific** | `.github/instructions/NAME.instructions.md` | Requisições no contexto de arquivos que casam com um path/glob específico |
| **Personal** / **Organization** | Configurado fora do repositório (perfil do usuário / configurações da organização) | Você, ou todos os membros da organização |

## Quando usar `copilot-instructions.md` (vs path-specific)

Segundo a documentação oficial (GitHub Blog), use `copilot-instructions.md` para instruções **gerais, válidas para todo o repositório**:

- Estilo de código e convenções de nomenclatura que se aplicam ao projeto inteiro
- Stack tecnológica e bibliotecas preferidas
- Padrões arquiteturais a seguir ou evitar
- Requisitos de segurança e abordagens de tratamento de erro
- Padrões de documentação

Reserve regras específicas de linguagem/tecnologia para arquivos `.instructions.md` separados em `.github/instructions/`, usando `applyTo` para direcionar a um subconjunto de arquivos.

## Formato

Markdown puro, em linguagem natural — sem frontmatter obrigatório. Espaços em branco entre instruções são ignorados; pode ser escrito como um parágrafo único, uma instrução por linha, ou separado por linhas em branco para legibilidade.

## Localização exigida (VS Code)

O arquivo `.github/copilot-instructions.md` **precisa** estar na pasta `.github` na raiz do workspace — não funciona em outro caminho.

## Nota: suporte a AGENTS.md

O VS Code também suporta o uso de um arquivo `AGENTS.md` para instruções always-on, como alternativa/complemento ao `copilot-instructions.md` — alinhando o Copilot ao mesmo padrão universal usado por Codex CLI, Antigravity IDE e outros.

## Boas práticas (regra de ouro do GitHub Blog)

Se o arquivo passar de ~4000 caracteres, priorize encurtá-lo identificando instruções redundantes — organize com headings e listas, e separe regras específicas de linguagem em arquivos `.instructions.md` por path em vez de inflar o arquivo único.

## Fontes

- https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot
- https://github.blog/ai-and-ml/github-copilot/unlocking-the-full-power-of-copilot-code-review-master-your-instructions-files
- https://code.visualstudio.com/docs/copilot/customization/custom-instructions
