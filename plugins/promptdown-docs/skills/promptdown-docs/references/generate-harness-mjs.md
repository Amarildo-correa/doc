`scripts/generate-harness.mjs` é o script Node.js chamado pelo `Makefile`
que lê `plugins/` e escreve os artefatos no destino correto de cada harness.

## Interface

```bash
node scripts/generate-harness.mjs <HARNESS>
# <HARNESS>: claude | cursor | antigravity | codex
```

## Lógica por harness

| HARNESS | Lê de | Escreve em | Transformação |
|---|---|---|---|
| `claude` | `plugins/*/rules/*.md` | `.claude/rules/*.md` | Copia; adiciona frontmatter `paths:` se ausente |
| `cursor` | `plugins/*/rules/*.md` | `.cursor/rules/*.mdc` | Converte `paths:` → `globs:` + `alwaysApply: true` |
| `antigravity` | `plugins/*/rules/*.md` | `.agents/rules/*.md` | Copia sem transformação |
| `codex` | `plugins/*/rules/*.md` | Injeta em `AGENTS.md` | Appenda seção `## Rules` |

## Dependências

Apenas `node:fs` e `node:path` — sem dependências externas.

## Idempotência

Rodar múltiplas vezes sobrescreve os artefatos com o conteúdo atual de
`plugins/` — nunca duplica.

## Fontes

- `plugins.md`
- `makefile.md`
