`GEMINI.md` é a configuração **nativa do Antigravity** (Antigravity-specific), distinta do padrão universal `AGENTS.md`.

## GEMINI.md vs AGENTS.md

| | `GEMINI.md` | `AGENTS.md` |
|---|---|---|
| Localização | Raiz do projeto ou `.gemini/` | Raiz do projeto |
| Propósito | Configuração específica do Antigravity para o modelo Gemini | Instruções de agente universais, cross-platform |
| Portabilidade | Só Antigravity | Funciona em Cursor, Windsurf, Claude Code (via import), Codex CLI, etc. |

**Prioridade de configuração (confirmada oficialmente): `AGENTS.md` → `GEMINI.md` → defaults internos.** Se ambos existirem, `AGENTS.md` tipicamente vence. A maioria dos desenvolvedores usa `AGENTS.md` por portabilidade entre ferramentas, reservando `GEMINI.md` só para o que é genuinamente específico do Antigravity.

## Exemplo de GEMINI.md

```markdown
# GEMINI.md

## Project Context
Next.js 14 e-commerce with Prisma/PostgreSQL

## Standards
- TypeScript strict mode
- All API routes need error boundaries
- Prefer server components

## Constraints
- No direct DB queries in components
- Use tRPC for type-safe APIs
```

## No padrão deste guia multi-agente

Para evitar duplicar contexto entre os dois arquivos, a convenção recomendada é:

```
@AGENTS.md

## Antigravity-specific
[quirks, contexto extra se necessário]
```

`GEMINI.md` importa o conteúdo universal de `AGENTS.md` e acrescenta só o que é específico do Antigravity — mesma lógica do `CLAUDE.md` do Claude Code.

## Fontes

- https://antigravity.md
- https://antigravity.google/docs/rules-workflows
