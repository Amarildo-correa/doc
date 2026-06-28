`.agents/plugins/` contém os plugins instalados localmente no workspace do Antigravity IDE — pacotes que agrupam skills, rules, MCP servers e hooks num único diretório instalável.

## Estrutura de um plugin (confirmada oficialmente)

```
plugins/<plugin-name>/
├── plugin.json        # marker obrigatório
├── mcp_config.json    # opcional — MCP servers
├── hooks.json         # opcional — hooks
├── skills/            # opcional — cada skill com seu SKILL.md
│   └── <skill-name>/
│       └── SKILL.md
└── rules/             # opcional — markdown de constraints/diretrizes
    └── <rule-name>.md
```

O campo `name` do `plugin.json` é opcional e usa o nome do diretório como default se omitido.

## Onde instalar plugins (confirmado oficialmente)

| Escopo | Localização |
|---|---|
| Workspace | `.agents/plugins/` ou `_agents/plugins/`, na raiz do workspace aberto |
| Global (todos os workspaces — **IDE**) | `~/.gemini/config/plugins/` |
| Global (todos os diretórios — **CLI `agy`**) | `~/.gemini/antigravity-cli/plugins/<plugin_name>/` |

A IDE escaneia automaticamente esses diretórios para descobrir e carregar customizações.

## Estrutura de plugin específica da CLI (`agy`)

A CLI standalone usa uma estrutura ligeiramente mais rica, incluindo uma pasta `agents/` para templates de definição de subagente:

```
~/.gemini/antigravity-cli/plugins/<plugin_name>/
├── plugin.json
├── mcp_config.json
├── hooks.json
├── skills/
├── agents/     # Optional subagent definition templates
└── rules/
```

## Duas formas de adicionar plugins

1. **Plugins empacotados (Build with Google)**: navegar até a página de Customizations da IDE e adicionar plugins criados pelo Google diretamente pela UI.
2. **Adição manual**: colocar a pasta do plugin em um dos locais designados acima — a IDE escaneia e carrega automaticamente.

## Correção sobre permissões

Plugins do Antigravity são **user-side** — as permissões (`Allow`/`Deny`/`Ask`) ficam do lado do usuário; manifestos de plugin **não declaram permissões próprias**. Um campo `"permissions": [...]` documentado por uma skill de comunidade de terceiros foi identificado como fabricado — nenhum plugin oficial do Google usa esse campo.

## Fontes

- https://antigravity.google/docs/plugins
- https://antigravity.google/docs/cli-plugins
- https://github.com/MemPalace/mempalace/blob/develop/hooks/antigravity/INVESTIGATION.md
