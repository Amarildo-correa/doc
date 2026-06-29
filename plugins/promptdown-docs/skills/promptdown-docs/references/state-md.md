`.specs/project/STATE.md` é um snapshot do estado atual do desenvolvimento — o que já está implementado, o que está em progresso, e bloqueios conhecidos. Diferente de `ROADMAP.md` (futuro) e `PROJECT.md` (propósito), este arquivo descreve o presente do projeto, e deve ser atualizado com frequência para permanecer confiável.

## Risco de desatualização

Como nenhum harness pesquisado carrega `.specs/` automaticamente (ver `specs-project.md`), `STATE.md` só é útil se for **ativamente mantido e referenciado** — um `STATE.md` desatualizado é pior que nenhum, porque o agente pode agir com base em informação que já não é verdade. Trate-o como parte do fluxo de trabalho (atualizar ao final de cada sessão relevante), não como documentação estática.

## Gaps identificados (pendente implementação)

| Item | Status |
|---|---|
| `Makefile` com targets `generate`/`generate-all` | ⚠️ Documentado, não implementado |
| `scripts/generate-harness.mjs` | ⚠️ Documentado, não implementado |
| `.claude/rules/` com 3 rules | ⚠️ Pasta ausente do scaffolding |
| `plugins/rules/{code-style,testing,security}.md` | ⚠️ Fontes canônicas não criadas |

## Fontes

- https://code.claude.com/docs/en/memory
