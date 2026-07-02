Roda o JSON Server, que transforma um arquivo JSON em uma API REST com CRUD completo (GET/POST/PUT/DELETE) sem escrever um servidor do zero. Quando o backend real entrar, só esta pasta muda — o frontend continua igual, pois fala com a API através de uma BASE_URL única.

## Papel na arquitetura geral

Esta pasta é a 'fronteira de contrato' entre o frontend (public/) e qualquer fonte de dados futura. O frontend nunca importa nada de dentro de 'api/' diretamente — a única ponte é HTTP, através de 'public/js/api.js' (no navegador) e de `env.API_URL` (a partir do `worker.js`, para SSR). Essa separação (pastas distintas, deploys distintos — Cloudflare para o frontend, Vultr só para `api/`) é o que torna trivial substituir o JSON Server por um backend real em outra linguagem sem tocar em uma linha do frontend.

```text
api/
├── server.js       # inicializa o JSON Server
├── database.json   # dados persistidos (volume Docker)
├── openapi.yaml     # contrato OpenAPI
└── middleware/      # middlewares customizados
```

## Ciclo de vida de uma requisição

- 1. A SPA hidratada (rodando no navegador) chama 'fetch' em 'api.js' apontando diretamente para a URL da API na Vultr, cross-origin (CORS habilitado em 'server.js') — não há mais um proxy Nginx no meio.
- 2. Os middlewares registrados em 'server.js' (logger → auth → request-delay) processam a requisição em cadeia, na ordem de 'server.use(...)'.
- 3. O router do JSON Server lê/escreve em 'database.json' e devolve JSON.
- 4. Em edições/criações de prompt, a API dispara um purge (e, em criações, um cache warming) via API do Cloudflare para a URL correspondente em '/prompts/:id' no Worker (ver 'worker-js.md') — mantendo o cache de edge coerente com o dado recém-gravado.

Além do fluxo acima (cliente → API), o `worker.js` também chama esta mesma API diretamente durante o SSR de `/prompts/:id`, usando a variável `API_URL` de `wrangler.toml` — sem passar pelo navegador.

## Por que JSON Server e não um servidor Express do zero?

Trade-off deliberado: um Express manual daria controle total (validação custom, paginação avançada, relações complexas), mas exigiria escrever cada rota CRUD à mão. O JSON Server gera essas rotas automaticamente a partir do schema do JSON, o que é ideal para prototipagem rápida e para o frontend não ficar bloqueado esperando um backend real. O custo é que regras de negócio mais sofisticadas exigem middlewares customizados (ver 'middleware/') em vez de código de rota nativo.

## Armadilha comum

Tratar este mock como definitivo. Toda lógica de negócio colocada aqui (ex.: validações específicas de domínio) precisa ser portada manualmente quando o backend real chegar — por isso o time mantém 'openapi.yaml' como contrato, não a implementação do JSON Server, como fonte da verdade.
