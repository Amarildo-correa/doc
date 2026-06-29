`Makefile` orquestra a geração de harnesses — lê a fonte única em `plugins/`
e distribui rules, skills e agents para cada ferramenta de IA no formato correto.

## Targets principais

| Target | O que faz |
|---|---|
| `make generate HARNESS=claude` | Gera `.claude/rules/`, `.claude/skills/`, `.claude/agents/` |
| `make generate HARNESS=cursor` | Gera `.cursor/rules/*.mdc` |
| `make generate HARNESS=antigravity` | Gera `.agents/rules/`, `.agents/skills/`, `.agents/plugins/` |
| `make generate HARNESS=codex` | Gera `.codex/skills/` |
| `make generate-all` | Executa todos os targets acima em sequência |

## Implementação

```makefile
generate-all:
	make generate HARNESS=claude
	make generate HARNESS=cursor
	make generate HARNESS=antigravity
	make generate HARNESS=codex

generate:
	node scripts/generate-harness.mjs $(HARNESS)
```

## Regra de ouro

Nunca editar os artefatos gerados (`.claude/rules/`, `.cursor/rules/`,
`.agents/rules/`) diretamente. A fonte de verdade vive apenas em `plugins/`.
Rodar `make generate-all` antes de cada commit garante paridade entre harnesses.

## Fontes

- `plugins.md`
- `generate-harness-mjs.md`
