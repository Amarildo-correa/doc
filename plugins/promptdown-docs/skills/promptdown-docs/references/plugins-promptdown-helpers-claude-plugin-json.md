`plugins/promptdown-helpers/.claude-plugin/plugin.json` é o marker específico
do Claude Code que habilita este plugin a ser distribuído via marketplace do
Claude Code ou instalado com `/plugin`.

## Conteúdo

```json
{ "name": "promptdown-helpers" }
```

## Diferença em relação ao `plugin.json` raiz

O `plugin.json` na raiz é lido pelo Antigravity IDE e pela convenção agnóstica
deste guia. O `.claude-plugin/plugin.json` é lido especificamente pelo Claude Code
para reconhecer o bundle como plugin instalável.

## Fontes

- `plugins.md`
