`.specs/project/` reúne os documentos de contexto do projeto como um todo — visão geral, roadmap e estado atual — distintos das especificações por feature em `.specs/features/`.

## Conteúdo

- `PROJECT.md` — descrição do projeto: o que faz e para quem.
- `ROADMAP.md` — plano de evolução a médio/longo prazo.
- `STATE.md` — snapshot do estado atual do desenvolvimento.

## Relação com .specs/DESIGN.md

`DESIGN.md`, já existente na raiz de `.specs/`, é a SSoT de decisões visuais (tokens, grid, componentes, acessibilidade). `.specs/project/` complementa isso com contexto de produto/roadmap, não de UI — juntos, ambos compõem a memória do projeto consumida por agentes de IA antes de decisões de implementação ou design.

## Como isso se relaciona com a memória nativa de cada harness

`.specs/` **não é** um padrão nativo de nenhum harness pesquisado — é uma convenção própria deste guia para organizar contexto de produto fora do arquivo de instruções principal (`AGENTS.md`). Ele alimenta `AGENTS.md`/`CLAUDE.md`/`GEMINI.md` por referência, em vez de duplicar o conteúdo:

| Harness | Como referenciaria `.specs/project/` |
|---|---|
| Claude Code | `@.specs/project/PROJECT.md` em `CLAUDE.md` (sintaxe de import confirmada oficialmente) |
| Antigravity IDE | `@.specs/project/PROJECT.md` em uma Rule ou no `AGENTS.md` (mesma sintaxe de `@menção`, confirmada oficialmente) |
| Codex CLI | Seção dedicada dentro do próprio `AGENTS.md` (Codex não documenta uma sintaxe de import de arquivo externo equivalente nesta pesquisa) |

## Fontes

- https://code.claude.com/docs/en/memory
- https://antigravity.google/docs/rules-workflows
