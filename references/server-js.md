Cria o servidor, aplica os middlewares padrão (CORS, logger), monta o router sob o prefixo '/api' e começa a escutar requisições.

## Anatomia do arquivo

```js
const jsonServer = require("json-server");
const auth = require("./middleware/auth");
const logger = require("./middleware/logger");
const requestDelay = require("./middleware/request-delay");

const server = jsonServer.create();
const router = jsonServer.router("api/database.json");
const middlewares = jsonServer.defaults(); // cors, static, logger nativo

// Ordem importa: cada server.use() é um elo da cadeia que a requisição
// atravessa antes de chegar ao router.
server.use(middlewares);
server.use(logger); // observa e mede, nunca bloqueia
server.use(requestDelay); // simula latência de rede real
server.use(auth); // pode interromper a cadeia com 401/403
server.use("/api", router); // só chega aqui se passou por todo o pipeline

server.listen(3001, () => {
    console.log("JSON Server rodando na porta 3001");
});
```

## Por que a ordem dos middlewares é a decisão de design mais importante aqui

Express (base do JSON Server) processa middlewares estritamente na ordem em que 'server.use()' é chamado. Colocar 'auth' antes de 'logger' faria requisições rejeitadas nunca aparecerem no log — uma escolha de observabilidade. Colocar 'requestDelay' depois de 'auth' atrasaria só requisições já autenticadas, evitando desperdiçar tempo simulando latência em chamadas que serão rejeitadas mesmo assim.

## Relação com o restante do projeto

É o único arquivo que conhece TODOS os middlewares simultaneamente — cada middleware individual ('auth.js', 'logger.js', 'request-delay.js') é deliberadamente cego aos outros, exportando apenas uma função '(req, res, next)'. Esse desacoplamento permite reordenar, remover ou adicionar um middleware editando só este arquivo, sem tocar nos demais.

## Escalabilidade e alternativas

Para uma API mock isso é suficiente. Um backend real tipicamente separaria isso em camadas (router por domínio, injeção de dependências, configuração via arquivo separado) — aqui a simplicidade de um único arquivo de bootstrap é proposital, já que toda esta pasta é descartável quando o backend de verdade for escrito.
