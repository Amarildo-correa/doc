`.claude/` é a pasta de configuração de **projeto** do Claude Code — ignorada por todos os outros agentes (Codex CLI, Cursor, Antigravity IDE, Copilot), que têm suas próprias pastas com convenções totalmente diferentes.

## Conteúdo

```
.claude/
├── settings.json        # configuração de projeto (commitada)
├── settings.local.json  # configuração local (gitignored automaticamente)
├── CLAUDE.md            # memória de projeto (alternativa ao CLAUDE.md na raiz)
├── rules/                # memória modular por tópico (*.md)
├── agents/               # subagentes do projeto
├── commands/              # slash commands do projeto
└── skills/                # skills do projeto (auto-descoberta via SKILL.md)
```

## Sistema de escopos do Claude Code

O Claude Code usa quatro escopos para decidir onde uma configuração se aplica e quem ela afeta. Quanto mais específico o escopo, maior a prioridade quando há conflito:

| Escopo | Localização | Afeta quem | Compartilhado com o time? |
|---|---|---|---|
| **Managed** (mais alta prioridade) | `managed-settings.json` em diretório de sistema | Todos os usuários da máquina | Sim (implantado por TI) |
| **Local** | `.claude/*.local.*` | Só você, neste repositório | Não (gitignored) |
| **Project** | `.claude/` no repositório | Todos os colaboradores deste repo | Sim (commitado no git) |
| **User** (mais baixa prioridade) | `~/.claude/` | Você, em todos os projetos | Não |

Ordem de precedência quando a mesma configuração existe em mais de um escopo: **Managed > linha de comando > Local > Project > User**.

`.claude/` (sem `~`) é especificamente o escopo **Project** — ideal para configurações que o time inteiro deve compartilhar (permissões, hooks, MCP servers, subagentes específicos da base de código). O equivalente em escopo **User**, válido em todos os seus projetos, é `~/.claude/` (note a ausência do prefixo de pasta do projeto).

## Como o Claude Code chega aqui

```
Claude Code  →  lê CLAUDE.md (ou .claude/CLAUDE.md)  →  carrega .claude/
```

`CLAUDE.md` na raiz (ou `.claude/CLAUDE.md`) é a memória textual do projeto; a configuração operacional (agents, commands, skills, settings, MCP) vive inteiramente dentro de `.claude/`, carregada à parte no início de cada sessão.

## settings.local.json e segredos

Diferente de `settings.json` (commitado, compartilhado com o time), `.claude/settings.local.json` é automaticamente adicionado ao `.gitignore` pelo próprio Claude Code quando é criado — é o lugar correto para preferências pessoais e experimentação que não devem ir para o controle de versão.

## Fontes

- https://code.claude.com/docs/en/settings
- https://code.claude.com/docs/en/memory
