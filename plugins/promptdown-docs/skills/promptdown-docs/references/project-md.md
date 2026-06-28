`.specs/project/PROJECT.md` descreve o projeto: o que ele faz e para quem — o equivalente, em escopo de produto, à seção `# Project` recomendada em `AGENTS.md`. Serve como ponto de partida para qualquer agente que precise entender o propósito de `promptdown-ui-v3` antes de planejar uma feature.

## Por que não escrever isso direto em AGENTS.md

`AGENTS.md` tem um limite prático de tamanho relevante — no Codex CLI, por exemplo, a cadeia concatenada de `AGENTS.md` (global + projeto + subpastas) está sujeita a um cap de ~32 KiB. Separar o contexto extenso de produto em `PROJECT.md` e referenciá-lo por import/menção mantém o `AGENTS.md` principal enxuto e focado em regras operacionais (comandos, boundaries), evitando que ele cresça até o limite com prosa de produto.

## Fontes

- https://developers.openai.com/codex/config-reference
