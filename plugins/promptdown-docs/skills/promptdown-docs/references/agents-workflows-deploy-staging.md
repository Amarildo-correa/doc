`.agents/workflows/deploy-staging.md` é um exemplo concreto de Workflow do Antigravity IDE, invocado via `/deploy-staging` no chat do agente.

```markdown
---
name: deploy-staging
description: Faz deploy do serviço no ambiente de staging
---

# Deploy Staging

## Steps

1. Rode os testes: `npm test`
2. Faça o build: `npm run build`
3. Envie para staging: `./scripts/deploy.sh staging`
4. Verifique o health check em https://staging.exemplo.com/health
```

## Por que esse design

- Workflows são sequências de passos no nível da **trajetória da tarefa** (em contraste com Rules, que são contexto persistente no nível do prompt — ver `agents-rules.md`).
- Limite confirmado oficialmente: **12.000 caracteres** por arquivo de workflow — passos devem ser concisos, não documentação extensa.
- O mesmo padrão de "rodar testes → build → deploy → verificar" aparece replicado como exemplo equivalente em `claude-commands-deploy-staging.md` (Claude Code) — ilustrando que o mesmo processo operacional pode ser expresso em formatos nativos diferentes por harness.

## Fontes

- https://antigravity.google/docs/rules-workflows
