Não usa React, Vue ou bundler obrigatório: a navegação é feita via History API, o estado via Proxy + pub/sub e os componentes via <template>. Em produção, o frontend e a API mock rodam em dois containers Docker (Nginx + JSON Server) numa VM Vultr, com deploy automático a cada push em 'main'.

## Visão geral da árvore

```text
promptdown-ui-v3/
├── public/        # frontend estático (Nginx)
├── api/           # backend mock (JSON Server)
├── tests/         # testes unitários (Vitest)
├── .specs/        # specs de features
├── scripts/       # automação de infraestrutura
├── notes/         # documentação interna
└── docker-compose.yml
```

## Convenções que valem para todo o projeto

- const/let, nunca var · === / !==, nunca == / !=
- JS puro com JSDoc opcional, sem TypeScript por padrão
- Nunca innerHTML com conteúdo dinâmico — sempre textContent ou <template>
- Roteamento via History API, nunca hash routing (#)
