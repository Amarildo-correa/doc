Durante o desenvolvimento, é fácil perder o rastro de quais requisições a SPA está disparando e quanto tempo cada uma demora. Um logger simples resolve isso sem precisar de uma ferramenta externa de observabilidade.

A técnica chave é capturar o tempo no início da requisição e medir a diferença quando a resposta termina, usando o evento 'finish' do objeto 'res'.

```js
function logger(req, res, next) {
    const start = Date.now(); // timestamp de chegada da requisição

    // "finish" dispara quando a resposta terminou de ser enviada ao
    // cliente — independente do status code (200, 404, 500, etc.)
    res.on("finish", () => {
        const ms = Date.now() - start;
        console.log(`${req.method} ${req.path} → ${res.statusCode} (${ms}ms)`);
    });

    next(); // não espera nada — segue o pipeline imediatamente
}

module.exports = logger;
```

## Por que não bloquear a requisição?

Note que 'logger' chama 'next()' imediatamente, sem esperar nada — ele só observa o ciclo de vida da resposta de forma assíncrona. Middlewares de log nunca devem atrasar a requisição que estão observando, sob pena de o próprio log distorcer a métrica que está medindo.

## Caso de uso real: depurando uma SPA lenta

Ao investigar por que 'prompt-list-view.js' parece travar ao carregar, o primeiro lugar a olhar é este log: ele revela se a lentidão é do backend (ex.: '(820ms)') ou do frontend (parsing/renderização). Sem essa instrumentação básica, a investigação vira tentativa e erro.

## Performance

O listener 'res.on("finish", ...)' tem custo desprezível — é apenas o registro de um callback, não uma operação bloqueante. Em alto volume de requisições, a única preocupação real seria o I/O do próprio 'console.log' (que é síncrono no Node em alguns ambientes); para produção, a evolução natural seria escrever em um stream assíncrono ou usar uma lib como 'pino'.

## Alternativa e trade-off

Bibliotecas como 'morgan' fariam o mesmo com menos código e formatos prontos (combined, dev, tiny). A escolha por um middleware caseiro aqui é didática e dá controle total do formato da linha de log — apropriado para um projeto pequeno onde uma dependência extra não se paga.
