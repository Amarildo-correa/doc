`.agents/agents/` — pasta para definições de agentes/personas por projeto no Antigravity IDE.

**Nota de confiabilidade**: a pesquisa oficial confirmou com clareza a existência de uma subpasta `agents/` dentro da estrutura de um **plugin** do Antigravity CLI (`agy`) — "Optional subagent definition templates", ao lado de `skills/`, `rules/`, `mcp_config.json` e `hooks.json`. Já uma pasta `.agents/agents/` solta no workspace (fora de um plugin), como descrita no guia genérico original, **não foi confirmada** como uma convenção documentada independente da IDE — o padrão mais robusto e citado pelos codelabs oficiais para "definir o time" de agentes é escrever personas direto em `AGENTS.md` (seção "Define the Team"), com Goals/Traits/Constraints por persona, e não uma pasta dedicada de arquivos por agente.

## Padrão real observado (codelab oficial)

```markdown
# AGENTS.md

## PM Agent
Goals: ...
Traits: ...
Constraints: ...

## Engineer Agent
Goals: ...
Traits: ...
Constraints: ...
```

Essa estrutura reduz drasticamente alucinações e garante que o agente siga estritamente o workflow desejado, segundo o codelab "Build Autonomous Developer Pipelines using agents.md and skills.md in Antigravity".

## Dentro de um plugin (CLI)

```
~/.gemini/antigravity-cli/plugins/<plugin_name>/
├── plugin.json
├── mcp_config.json
├── hooks.json
├── skills/
├── agents/     # Optional subagent definition templates
└── rules/
```

## Fontes

- https://codelabs.developers.google.com/autonomous-ai-developer-pipelines-antigravity
- https://antigravity.google/docs/cli-plugins
