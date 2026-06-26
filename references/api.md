Roda o JSON Server, que transforma um arquivo JSON em uma API REST com CRUD completo (GET/POST/PUT/DELETE) sem escrever um servidor do zero. Quando o backend real entrar, só esta pasta muda — o frontend continua igual, pois fala com a API através de uma BASE_URL única.

## Papel na arquitetura geral

Esta pasta é a 'fronteira de contrato' entre o frontend (public/) e qualquer fonte de dados futura. O frontend nunca importa nada de dentro de 'api/' diretamente — a única ponte é HTTP, através de 'public/js/api.js'. Essa separação física (duas pastas, dois Dockerfiles, dois containers) é o que torna trivial substituir o JSON Server por um backend real em outra linguagem sem tocar em uma linha do frontend.

```text
api/
├── server.js       # inicializa o JSON Server
├── database.json   # dados persistidos (volume Docker)
├── openapi.yaml     # contrato OpenAPI
└── middleware/      # middlewares customizados
```

## Ciclo de vida de uma requisição

- 1. A SPA chama 'fetch' em 'api.js' apontando para '/api/...'.
- 2. O Nginx (container 'frontend') faz proxy_pass para o container 'json-server'.
- 3. Os middlewares registrados em 'server.js' (logger → auth → request-delay) processam a requisição em cadeia, na ordem de 'server.use(...)'.
- 4. O router do JSON Server lê/escreve em 'database.json' e devolve JSON.

## Por que JSON Server e não um servidor Express do zero?

Trade-off deliberado: um Express manual daria controle total (validação custom, paginação avançada, relações complexas), mas exigiria escrever cada rota CRUD à mão. O JSON Server gera essas rotas automaticamente a partir do schema do JSON, o que é ideal para prototipagem rápida e para o frontend não ficar bloqueado esperando um backend real. O custo é que regras de negócio mais sofisticadas exigem middlewares customizados (ver 'middleware/') em vez de código de rota nativo.

## Armadilha comum

Tratar este mock como definitivo. Toda lógica de negócio colocada aqui (ex.: validações específicas de domínio) precisa ser portada manualmente quando o backend real chegar — por isso o time mantém 'openapi.yaml' como contrato, não a implementação do JSON Server, como fonte da verdade.
