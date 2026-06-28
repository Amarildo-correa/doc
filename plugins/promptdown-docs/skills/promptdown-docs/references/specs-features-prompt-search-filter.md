`promptdown-ui-v3/.specs/features/prompt-search-filter/` é um exemplo concreto de pasta de feature dentro de `.specs/features/` — preenchendo o padrão `[feature]/spec.md` (descrito de forma abstrata em `specs-features.md`) com um caso real do projeto: busca e filtro de prompts por título ou tag.

## Conteúdo

```
.specs/features/prompt-search-filter/
└── spec.md
```

Só `spec.md` está presente — sem `design.md` nem `tasks.md`. Isso é intencional, não uma omissão: pelo processo de auto-sizing do skill `tlc-spec-driven`, esta feature foi classificada como **Medium** (feature clara, reaproveita padrões já existentes do projeto — `store.js`, `router.js`, sem decisão arquitetural nova, poucos passos óbvios de implementação). Nesse porte, **Design é pulado** e **Tasks fica implícita**, evitando burocracia desproporcional ao tamanho real do trabalho.

## Quando uma feature pediria design.md e tasks.md

Segundo o mesmo skill, isso só se justifica em features **Large** (múltiplos componentes, precisa de arquitetura explícita) ou **Complex** (ambiguidade real, domínio novo, exige pesquisa). Ver `specs-features.md` para o padrão completo da pasta quando os três arquivos são necessários.

## Fontes

- Skill `tlc-spec-driven` — regra de auto-sizing (Specify sempre obrigatório; Design/Tasks pulados conforme escopo)
