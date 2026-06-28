`.specs/features/` reúne especificações por feature, escritas para guiar desenvolvimento dirigido por agentes de IA.

## Padrão de nomenclatura

```
.specs/features/[feature]/
├── spec.md       # requisitos e critérios de aceite da feature
├── design.md     # decisões de design/implementação específicas dela
└── tasks.md      # quebra em tarefas executáveis
```

## Por que separado de .specs/project/

`.specs/project/` documenta o projeto como um todo (visão, roadmap, estado); `.specs/features/[feature]/` documenta uma feature específica em isolamento — permitindo que um agente carregue só o contexto da feature em que está trabalhando, sem precisar ler o histórico completo do projeto.

## Paralelo com mecanismos nativos de planejamento

Esse padrão de `spec.md` → `design.md` → `tasks.md` por feature ecoa, em espírito, dois mecanismos nativos confirmados na pesquisa:

- **Claude Code — Plan Mode**: o subagente `Plan` pesquisa o código antes de apresentar um plano de implementação, sem fazer edições até a aprovação — `spec.md`/`design.md` podem servir de input para esse plano em vez de o agente reconstruir o contexto do zero a cada sessão.
- **Antigravity IDE — modo "Planning"**: o agente organiza o trabalho em grupos de tarefas e produz "Artifacts" antes de executar, indicado para pesquisa profunda, tarefas complexas ou trabalho colaborativo (em contraste com o modo "Fast", para tarefas simples e diretas).

Nos dois casos, ter `spec.md`/`design.md`/`tasks.md` já escritos por feature reduz o trabalho de descoberta que esses modos de planejamento fariam do zero.

## Fontes

- https://code.claude.com/docs/en/sub-agents (seção "Built-in subagents" / Plan)
- https://medium.com/google-cloud/tutorial-getting-started-with-google-antigravity-b5cc74c103c2
