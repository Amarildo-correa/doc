`.claude/commands/deploy-staging.md` é um exemplo concreto de slash command de projeto do Claude Code, invocado digitando `/deploy-staging` no input do agente.

```markdown
---
allowed-tools: Bash(npm run test:*), Bash(npm run build), Bash(./scripts/deploy.sh:*)
argument-hint: [environment]
description: Deploy the current branch to staging with validation
---

## Context

- Current branch: !`git branch --show-current`
- Working tree status: !`git status --short`

## Your task

1. Run the test suite: `npm run test`
2. Build the project: `npm run build`
3. Deploy to staging: `./scripts/deploy.sh staging`
4. Report the deployment URL and verify the health check endpoint responds.

Stop and report immediately if any step fails — do not proceed to the next step.
```

## Por que esse design

- **`allowed-tools` restrito** a só os comandos necessários (`npm run test:*`, `npm run build`, `./scripts/deploy.sh:*`) — o comando não pode rodar bash arbitrário, só esses três.
- **Bloco `!` de contexto** (`!`git branch --show-current``, `!`git status --short``) injeta o estado atual do git antes do agente decidir o que fazer — mesmo padrão do exemplo oficial de "Create a git commit".
- **`argument-hint: [environment]`** documenta, no autocomplete, que o comando aceita um argumento opcional de ambiente (mesmo que este exemplo específico só implemente `staging`).

Ver `claude-commands.md` para a referência completa de frontmatter e sintaxe de argumentos (`$ARGUMENTS`, `$1`/`$2`).

## Fontes

- https://code.claude.com/docs/en/slash-commands
