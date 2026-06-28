`plugins/` é a FONTE — escrita uma única vez e gerada para todos os harnesses (Claude Code, Codex CLI, Cursor, Antigravity IDE) via Makefile. É aqui que skills, rules, hooks e agents agnósticos de harness são definidos.

## Estrutura de um plugin (agnóstica, fonte de verdade)

```
plugins/[plugin-name]/
├── plugin.json          # marker obrigatório: { "name": "plugin-name" }
├── .claude-plugin/
│   └── plugin.json      # marker específico do Claude Code
├── mcp_config.json      # MCP servers do plugin (Antigravity, opcional)
├── hooks.json           # Hooks do plugin (Antigravity, opcional)
├── agents/
│   └── [agent-name].md
├── commands/
│   └── [command].md
├── rules/               # Rules para Antigravity
│   └── [rule-name].md
└── skills/
    └── [skill-name]/
        ├── SKILL.md
        └── references/
            └── style-guide.md
```

## plugin.json

```json
{ "name": "my-plugin" }
```

O campo `name` é opcional — default é o nome da pasta. Confirmado oficialmente para o Antigravity (`antigravity.google/docs/plugins`).

## Como cada harness realmente consome "plugins" (pesquisa confirmou diferenças importantes)

| Harness | Conceito de plugin | Estrutura real confirmada |
|---|---|---|
| **Antigravity IDE** | Plugin = pasta com `plugin.json` + `skills/`, `rules/`, `mcp_config.json`, `hooks.json` | Confirmado oficialmente — bate com a estrutura agnóstica acima |
| **Antigravity CLI (`agy`)** | Mesma ideia, + pasta `agents/` para templates de subagente | `~/.gemini/antigravity-cli/plugins/<plugin_name>/` |
| **Claude Code** | Plugin = bundle distribuível via marketplace, com `.claude-plugin/plugin.json` como marker, podendo conter `agents/`, `commands/`, `skills/`, hooks em `settings.json` do próprio plugin | Distribuído e instalado via `/plugin` e marketplaces — mecanismo mais amplo que "gerar arquivos a partir de uma fonte agnóstica" |
| **Codex CLI** | Plugins/marketplace próprios, com comandos de CLI para adicionar/listar/remover/fixar fontes (aceita `owner/repo`, URLs, refs via `--ref`) | Não documentado um schema de pasta idêntico ao do Claude Code/Antigravity nesta pesquisa |
| **Cursor** | Não tem conceito de "plugin" equivalente — só Rules por arquivo `.mdc` | — |

## Geração por harness via Makefile

```makefile
make generate HARNESS=claude        →  plugins/ → .claude/agents .claude/skills
make generate HARNESS=codex         →  plugins/ → .codex/agents  .codex/skills
make generate HARNESS=antigravity   →  plugins/ → .agents/skills .agents/rules
                                                   .agents/workflows .agents/plugins
make generate HARNESS=cursor        →  plugins/ → .cursor/rules/*.mdc
make generate-all                   →  todos acima (rodar antes de cada commit)
```

Este Makefile é uma convenção **deste guia**, não um padrão oficial de nenhum dos harnesses — cada um tem seu próprio mecanismo nativo de plugin/marketplace (ver tabela acima); gerar arquivos estáticos a partir de `plugins/` é uma forma prática de manter paridade entre eles sem depender de um marketplace específico.

## Regra de ouro

Nunca editar os artefatos gerados (`.claude/`, `.codex/`, `.agents/`, `.cursor/rules/*.mdc`) diretamente. A fonte de verdade vive apenas em `plugins/`.

## Fontes

- https://antigravity.google/docs/plugins
- https://antigravity.google/docs/cli-plugins
- https://code.claude.com/docs/en/skills (seção "Distribute Skills")
- https://developers.openai.com/codex/cli/reference
