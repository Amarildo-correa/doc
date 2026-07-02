`promptdown-ui-v3/.specs/features/repo-scaffolding/` é um exemplo concreto de feature **Large** dentro de `.specs/features/` — em contraste com `prompt-search-filter/` (Medium, só `spec.md`), esta tem os três arquivos completos.

## Conteúdo

```
.specs/features/repo-scaffolding/
├── spec.md     # user stories por subsistema (frontend, backend mock, multi-agente, infra)
├── design.md   # arquitetura do gerador de scaffold + Knowledge Verification Chain aplicada
└── tasks.md    # 12 tasks, 1 sequencial (raiz) + 11 paralelas (uma por subsistema)
```

## Por que esta é Large, e `prompt-search-filter` é Medium

| Critério | `prompt-search-filter` (Medium) | `repo-scaffolding` (Large) |
|---|---|---|
| Decisão arquitetural nova | Nenhuma — reaproveita `store.js`/`router.js` como já documentados | Sim, ainda que pequena — precisa de um script gerador novo (`scaffold-from-skill.mjs`) e uma estratégia de placeholder |
| Quantidade de componentes tocados | 1-2 (`prompt-list-view.js`, `store.js`) | ~12 subsistemas independentes (praticamente toda a árvore do `AGENTS.md`) |
| Precisa de Design formal? | Não — design inline durante Execute | Sim — arquitetura do gerador, reuso de código, estratégia de erro |
| Precisa de Tasks formal? | Não — passos óbvios e poucos | Sim — 12 tasks, paralelizáveis, com validação de granularidade |

Note que **nenhuma das duas precisou de `discuss.md`** (zona cinzenta exigindo decisão do usuário) — `repo-scaffolding` é grande em volume, mas não ambígua: toda a "decisão de produto" já está feita nos ~90 `references/*.md` que essa feature só está organizando em pastas/arquivos reais. É um bom contraste com o que tornaria uma feature **Complex** de fato (ver a discussão sobre o "Prompt Playground" — ali sim haveria zonas cinzentas genuínas, como onde a chave de API de um LLM externo deveria viver).

## Fontes

- Skill `tlc-spec-driven` — tabela de auto-sizing (Small/Medium/Large/Complex)
