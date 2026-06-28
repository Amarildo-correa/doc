`.claude/skills/tavily-search/SKILL.md` é um exemplo **real**, não hipotético — uma skill efetivamente instalada em `~/.claude/skills/tavily-search/` nesta máquina, que ensina o Claude a buscar na web via `tvly search` (Tavily CLI).

```yaml
---
name: tavily-search
description: |
  Search the web with LLM-optimized results via the Tavily CLI. Use this skill when the user wants to search the web, find articles, look up information, get recent news, discover sources, or says "search for", "find me", "look up", "what's the latest on", or needs current information from the internet. ...
allowed-tools: Bash(tvly *)
---

# tavily search

Web search returning LLM-optimized results with content snippets and relevance scores.
```

O corpo do `SKILL.md` documenta o comando `tvly search "query" --json`, opções (`--depth`, `--max-results`, `--time-range`, `--include-domains`, etc.) e referencia skills relacionadas (`tavily-extract`, `tavily-research`) com links relativos — exemplo direto de **progressive disclosure** e de "See also" entre skills.

## Por que é um bom exemplo do padrão

- `allowed-tools: Bash(tvly *)` restringe a skill a só rodar o binário `tvly` — ela não pode rodar bash arbitrário.
- A `description` (truncada acima) lista explicitamente termos-gatilho ("search for", "find me", "what's the latest on") — o tipo de palavra-chave que o Claude usa para decidir ativar a skill automaticamente.
- Referencia skills companheiras por link relativo (`../tavily-extract/SKILL.md`) em vez de duplicar conteúdo — mesmo princípio de "manter referências a um nível de profundidade" recomendado na documentação oficial.

Esta mesma skill (e suas 7 companheiras: `tavily-cli`, `tavily-extract`, `tavily-crawl`, `tavily-map`, `tavily-research`, `tavily-dynamic-search`, `tavily-best-practices`) também foi replicada em `~/.codex/skills/` e `~/.gemini/antigravity/skills/` nesta máquina — ver `codex-skills-tavily-cli.md` para o equivalente no Codex CLI.

## Fontes

- https://code.claude.com/docs/en/skills
