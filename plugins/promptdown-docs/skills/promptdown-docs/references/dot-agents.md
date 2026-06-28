`.agents/` é a pasta de configuração de **workspace** do **Google Antigravity IDE** — ignorada por todos os outros agentes. "Antigravity" tem duas variantes distintas com convenções de path ligeiramente diferentes: a **IDE** (extensão completa) e o **Antigravity CLI** (`agy`, ferramenta de terminal standalone) — esta documentação cobre principalmente a IDE, com notas onde a CLI difere.

## Conteúdo

```
.agents/
├── skills/      # Skills de workspace
├── rules/       # Rules de workspace
├── workflows/   # Workflows de workspace (.md, invocados via /workflow-name)
├── plugins/     # Plugins instalados localmente (ou _agents/plugins/)
└── hooks.json   # Hooks de workspace (fora de qualquer plugin)
```

Nota oficial: "Antigravity now defaults to `.agents/skills`, but still maintains backward support for `.agent/skills`" — o mesmo vale para `rules/`. Ou seja, tanto a pasta no singular (`.agent/`) quanto a no plural (`.agents/`) funcionam, com `.agents/` sendo o padrão atual.

## Como o Antigravity IDE chega aqui

```
Antigravity IDE  →  lê AGENTS.md → GEMINI.md → defaults internos  →  carrega .agents/
```

Confirmado pela documentação oficial: a prioridade de configuração é **AGENTS.md → GEMINI.md → defaults internos**. Se ambos existirem, `AGENTS.md` tipicamente vence — a maioria dos desenvolvedores usa `AGENTS.md` por portabilidade entre ferramentas (Cursor, Windsurf, etc.), reservando `GEMINI.md` para configuração específica do Antigravity.

## Caminhos por escopo — IDE vs CLI (correção importante)

A pesquisa oficial revela que os caminhos **globais** diferem entre a IDE e a CLI standalone — isto corrige uma imprecisão do guia genérico original, que misturava os dois:

| Componente | Workspace (ambos) | Global — **IDE** | Global — **CLI (`agy`)** |
|---|---|---|---|
| Skills | `.agents/skills/` | `~/.gemini/config/skills/` | `~/.gemini/antigravity-cli/skills/` |
| Plugins | `.agents/plugins/` ou `_agents/plugins/` | `~/.gemini/config/plugins/` | `~/.gemini/antigravity-cli/plugins/` |
| Hooks | `.agents/hooks.json` | `~/.gemini/config/hooks.json` | configurado em `settings.json` ou `hooks.json` de plugin |
| Rules | `.agents/rules/` | — (gerenciado via UI/Settings) | — |

(O guia genérico original citava `~/.gemini/antigravity/skills/` como caminho global — esse caminho **não foi confirmado** na documentação oficial pesquisada; os caminhos reais são os da tabela acima.)

## Fontes

- https://antigravity.google/docs/skills
- https://antigravity.google/docs/rules-workflows
- https://antigravity.google/docs/plugins
- https://antigravity.google/docs/hooks
- https://antigravity.google/docs/cli-plugins
