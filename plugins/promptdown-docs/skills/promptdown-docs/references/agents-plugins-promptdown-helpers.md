`.agents/plugins/promptdown-helpers/` é um exemplo concreto de plugin instalado no workspace do Antigravity IDE — a versão **gerada** (não editar diretamente) do plugin-fonte documentado em `plugins-promptdown-helpers.md`.

```
.agents/plugins/promptdown-helpers/
├── plugin.json
├── skills/
│   └── format-prompt-card/
│       └── SKILL.md
└── rules/
    └── card-naming.md
```

```json
// plugin.json
{ "name": "promptdown-helpers" }
```

## Por que esse design

- Reflete exatamente a estrutura confirmada oficialmente para plugins do Antigravity IDE (`plugin.json` obrigatório + `skills/`/`rules/`/`mcp_config.json`/`hooks.json` opcionais) — ver `agents-plugins.md`.
- Está em `.agents/plugins/` (escopo de **workspace** — só ativo neste projeto), em vez de `~/.gemini/config/plugins/` (escopo global) — porque `format-prompt-card` e `card-naming` são específicos das convenções de `promptdown-ui-v3` (cards de prompt), não úteis em outros projetos.
- Esta pasta seria recriada pelo Makefile (`make generate HARNESS=antigravity`) a partir de `plugins/promptdown-helpers/` — nunca editada manualmente.

## Fontes

- https://antigravity.google/docs/plugins
