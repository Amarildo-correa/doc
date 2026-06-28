`.codex/skills/tavily-cli/SKILL.md` é o equivalente, no Codex CLI, do mesmo exemplo real documentado em `claude-skills-tavily-search.md` — a mesma pasta de skill (`tavily-cli`, com instruções gerais sobre o comando `tvly`) foi copiada desta máquina para `~/.codex/skills/tavily-cli/` além de `~/.claude/skills/tavily-cli/`.

## O que isso demonstra na prática

O formato `SKILL.md` (Markdown + frontmatter YAML com `name`/`description`) é **portável entre Claude Code e Codex CLI** sem qualquer adaptação — a mesma pasta de skill funciona em ambos, porque os dois implementam o mesmo conceito de progressive disclosure (carregar só `name`+`description` no início, conteúdo completo só quando invocada).

```yaml
---
name: tavily-cli
description: |
  Web search, content extraction, crawling, and deep research via the Tavily CLI. Use this skill whenever the user wants to search the web, find articles, ...
allowed-tools: Bash(tvly *)
---

# Tavily CLI

Web search, content extraction, site crawling, URL discovery, and deep research.
Run `tvly --help` or `tvly <command> --help` for full option details.
```

## Diferença prática de localização

| Harness | Caminho usado nesta máquina |
|---|---|
| Claude Code | `~/.claude/skills/tavily-cli/SKILL.md` |
| Codex CLI | `~/.codex/skills/tavily-cli/SKILL.md` |

Mesmo conteúdo, duas cópias — porque cada harness escaneia sua própria pasta de skills; não há um mecanismo de symlink/compartilhamento nativo confirmado entre os dois.

## Fontes

- https://developers.openai.com/codex/skills
- https://code.claude.com/docs/en/skills
