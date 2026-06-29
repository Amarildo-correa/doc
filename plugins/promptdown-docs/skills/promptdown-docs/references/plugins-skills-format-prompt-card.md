`plugins/promptdown-helpers/skills/format-prompt-card/` é a skill-fonte do plugin
— escrita uma vez aqui, gerada para `.claude/skills/` e `.codex/skills/` via Makefile.

## Estrutura

```
format-prompt-card/
├── SKILL.md
└── references/
    └── style-guide.md
```

## SKILL.md (frontmatter)

```yaml
---
name: format-prompt-card
description: >
  Formata os dados de um prompt no padrão de card usado em prompt-list-view.js
  e card.js. Use quando criar ou normalizar dados de prompt para exibição em card.
---
```

## Onde é gerado

```
make generate HARNESS=claude   → .claude/skills/format-prompt-card/
make generate HARNESS=codex    → .codex/skills/format-prompt-card/
```

## Fontes

- `plugins-promptdown-helpers.md`
- `plugins.md`
