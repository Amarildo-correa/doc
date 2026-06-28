`.cursor/` é a pasta de configuração de projeto do **Cursor** — ignorada por todos os outros agentes. Diferente do Claude Code/Codex/Antigravity, o Cursor não usa o conceito de "Skill" (Markdown + frontmatter `description` para auto-descoberta) — usa **Rules**, um mecanismo de injeção de contexto baseado em arquivos `.mdc`.

## Conteúdo

```
.cursor/
└── rules/
    ├── general.mdc
    ├── frontend.mdc
    └── backend.mdc
```

## Como o Cursor chega aqui

```
Cursor  →  lê .cursor/rules/  →  aplica por tipo de regra (Always / Glob / Manual / Agent-requested)
```

O Cursor não tem um arquivo-ponte central de contexto de projeto como `AGENTS.md` — cada regra decide por si mesma quando entra em jogo, via o campo `alwaysApply` ou `globs` no seu próprio frontmatter.

## Três escopos de Rules

| Escopo | Onde vive | Observação |
|---|---|---|
| **Project** | `.cursor/rules/*.mdc` no repositório | Versionado no git, escopo de todo o repositório (subpastas com seu próprio `.cursor/rules/` podem restringir regras a essa subárvore, segundo prática da comunidade) |
| **User** | Configurado nas Settings do Cursor (não é um arquivo de projeto) | Pessoal, todos os projetos abertos no Cursor |
| **Team** | Gerenciado via dashboard/organização do Cursor | Compartilhado entre membros do time |

A documentação oficial (`cursor.com/docs/rules`) lista os três tipos, mas **não publica uma tabela formal de precedência** entre eles — a prática da comunidade (não confirmada oficialmente) reporta a ordem Team > Project > User quando há conflito.

## Migração de `.cursorrules` (legado)

O arquivo único `.cursorrules` na raiz do projeto é o formato **legado**, hoje com comportamento inconsistente em modo agente — a recomendação é migrar seu conteúdo para `.cursor/rules/*.mdc`.

## Fontes

- https://cursor.com/docs/rules
