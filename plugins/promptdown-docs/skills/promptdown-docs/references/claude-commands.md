`.claude/commands/` define os **slash commands customizados** do Claude Code para este projeto — prompts frequentemente usados, definidos como arquivos Markdown, invocados digitando `/nome-do-comando` no input do agente.

## Tipos de comando por escopo

| Tipo | Localização | Como aparece em `/help` |
|---|---|---|
| **Project** | `.claude/commands/` | sufixo "(project)" — commitado, compartilhado com o time |
| **Personal** | `~/.claude/commands/` | sufixo "(user)" — disponível em todos os seus projetos |

Se um comando de projeto e um pessoal têm o mesmo nome, o de projeto tem precedência e o pessoal é silenciosamente ignorado.

## Sintaxe

```
/<command-name> [arguments]
```

O `<command-name>` vem do nome do arquivo Markdown (sem `.md`). Namespacing por subpastas é suportado: `.claude/commands/frontend/component.md` cria `/component` com descrição "(project:frontend)".

## Argumentos

**Todos de uma vez com `$ARGUMENTS`:**

```bash
echo 'Fix issue #$ARGUMENTS following our coding standards' > .claude/commands/fix-issue.md
# uso: /fix-issue 123 high-priority → $ARGUMENTS = "123 high-priority"
```

**Individuais com `$1`, `$2`, etc. (como em scripts shell):**

```bash
echo 'Review PR #$1 with priority $2 and assign to $3' > .claude/commands/review-pr.md
# uso: /review-pr 456 high alice → $1="456" $2="high" $3="alice"
```

## Frontmatter

| Campo | Propósito | Padrão |
|---|---|---|
| `allowed-tools` | Ferramentas que o comando pode usar | Herda da conversa |
| `argument-hint` | Dica de argumentos mostrada no autocomplete (ex.: `[pr-number] [priority]`) | Nenhum |
| `description` | Descrição breve do comando | Primeira linha do prompt |
| `model` | Modelo específico | Herda da conversa |
| `context: fork` | Roda o comando em um subagente isolado, com histórico próprio | Inline (sem fork) |
| `agent` | Tipo de agente a usar com `context: fork` | `general-purpose` |
| `disable-model-invocation` | Impede a ferramenta `Skill` de chamar este comando programaticamente | `false` |
| `hooks` | Hooks escopados à execução deste comando | Nenhum |

## Execução de comandos bash dentro do comando

Use `!` para executar bash antes do comando rodar — a saída entra no contexto. Requer `allowed-tools` com `Bash`:

```markdown
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*)
description: Create a git commit
---

## Context
- Current git status: !`git status`
- Current git diff: !`git diff HEAD`
- Current branch: !`git branch --show-current`

## Your task
Based on the above changes, create a single git commit.
```

## Referência de arquivos com `@`

```markdown
Review the implementation in @src/utils/helpers.js
Compare @src/old-version.js with @src/new-version.js
```

## A ferramenta `Skill`

Slash commands customizados e Skills são invocáveis programaticamente pela mesma ferramenta `Skill` (em versões antigas, isso era uma ferramenta separada chamada `SlashCommand`, hoje fundida). Comandos built-in como `/compact` e `/init` **não** estão disponíveis por essa via.

Para um comando ser invocável pela ferramenta `Skill`, ele precisa ter `description` no frontmatter. Para bloquear isso explicitamente, adicione `disable-model-invocation: true`.

## Slash commands vs Skills — quando usar cada um

| Aspecto | Slash Commands | Agent Skills |
|---|---|---|
| Complexidade | Prompts simples | Capacidades complexas |
| Estrutura | Um arquivo `.md` | Diretório com `SKILL.md` + recursos |
| Descoberta | Invocação explícita (`/comando`) | Automática (baseada em contexto) |
| Arquivos | Só um | Múltiplos arquivos, scripts, templates |

Use slash commands para prompts que você invoca repetidamente e cabem em um arquivo; use Skills quando o Claude deve descobrir a capacidade automaticamente, ou quando o workflow precisa de múltiplos arquivos/scripts.

## Comandos de plugins

Plugins podem fornecer slash commands próprios, em `commands/` na raiz do plugin. Usam o formato `/plugin-name:command-name` (prefixo opcional, salvo conflito de nomes).

## Comandos expostos por servidores MCP

MCP servers podem expor *prompts* como slash commands, descobertos dinamicamente: `/mcp__<server-name>__<prompt-name> [arguments]`.

## Fontes

- https://code.claude.com/docs/en/slash-commands
