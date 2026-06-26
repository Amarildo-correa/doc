Cada arquivo YAML aqui descreve um workflow: quando disparar (push, pull request, etc.) e quais passos (jobs) executar — como instalar dependências, rodar testes e fazer deploy.

```yaml
on:
    push:
        branches: [main]

jobs:
    test: ...
    deploy: ...
```
