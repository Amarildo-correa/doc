`plugins/promptdown-helpers/` é um exemplo concreto de plugin-fonte deste projeto — escrito uma vez aqui, depois gerado para cada harness via Makefile (ver `plugins.md`).

```
plugins/promptdown-helpers/
├── plugin.json
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── format-prompt-card/
│       ├── SKILL.md
│       └── references/
│           └── style-guide.md
└── rules/
    └── card-naming.md
```

```json
// plugin.json
{ "name": "promptdown-helpers" }
```

```yaml
# skills/format-prompt-card/SKILL.md (frontmatter)
---
name: format-prompt-card
description: >
  Formata os dados de um prompt no padrão de card usado em prompt-list-view.js
  e card.js. Use quando criar ou normalizar dados de prompt para exibição em card.
---
```

## Por que esse exemplo concreto, e não genérico

Diferente do exemplo abstrato `[plugin-name]/[skill-name]/` usado no restante da documentação, este plugin usa nomes reais e específicos de `promptdown-ui-v3`: `format-prompt-card` referencia diretamente os componentes já documentados em `card-js.md` e `prompt-list-view-js.md` — mostrando como a convenção `plugins/` se aplica a este projeto específico, não só como teoria.

## Geração para cada harness

```makefile
make generate HARNESS=claude        # → .claude/agents .claude/skills/format-prompt-card
make generate HARNESS=codex         # → .codex/skills/format-prompt-card
make generate HARNESS=antigravity   # → .agents/plugins/promptdown-helpers/ (ver agents-plugins-promptdown-helpers.md)
make generate HARNESS=cursor        # → .cursor/rules/card-naming.mdc
```

## Fontes

- https://antigravity.google/docs/plugins
- https://code.claude.com/docs/en/skills
