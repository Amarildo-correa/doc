Organizado por responsabilidade única: roteamento, estado, comunicação com a API e entry point — mais duas subpastas de reuso.

```text
js/
├── app.js        # entry point
├── router.js     # History API
├── store.js      # estado global
├── api.js        # chamadas HTTP
├── lib/          # funções puras
├── views/        # telas da SPA
└── components/   # UI reutilizável
```

## O fluxo de dependências entre essas peças

O 'app.js' inicializa 'router.js', que por sua vez instancia views de 'views/'. Cada view lê e escreve na 'store.js' e busca dados via 'api.js'. Componentes em 'components/' são usados pelas views, mas nunca o contrário — eles não conhecem a store nem a API, o que os torna reutilizáveis em qualquer contexto. 'lib/' fica na base de tudo: funções puras sem dependências de DOM ou estado, usadas por qualquer camada acima.

```text
app.js → router.js → views/* → store.js
                          ↘ api.js
                          ↘ components/* → lib/*
```

## Por que essa direção de dependência importa

Manter 'components/' e 'lib/' cegos à store e ao router é o que evita acoplamento circular. Se um componente importasse a store diretamente, ele deixaria de ser reutilizável fora do contexto desta aplicação específica — uma violação do princípio de responsabilidade única que dificultaria testes isolados e reuso futuro.

## Convenção de módulos ES nativos

Todo arquivo aqui usa 'import'/'export' nativo do navegador, sem bundler. Isso exige que toda referência a outro módulo seja um caminho relativo explícito (ex.: '../store.js', com extensão '.js' obrigatória) — diferente de ambientes com bundler, onde extensões e resolução de caminho podem ser implícitas.
