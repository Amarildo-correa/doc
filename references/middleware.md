Útil para comportamento que o JSON Server não tem nativamente: autenticação simulada, validações extras, atraso artificial de rede, logs personalizados.

## O contrato comum a todo middleware Express

Toda função aqui dentro segue a mesma assinatura: '(req, res, next) => void'. 'req' e 'res' dão acesso à requisição/resposta; 'next()' é o que decide se a cadeia continua. Esse contrato uniforme é o que permite ao 'server.js' compor middlewares como peças de Lego, em qualquer ordem.

```js
function logRequest(req, res, next) {
    console.log(`${req.method} ${req.path}`); // efeito colateral: log
    next(); // sempre chama next() — não bloqueia ninguém
}

module.exports = logRequest;
```

## Duas categorias de middleware nesta pasta

- Observadores (logger.js): nunca interrompem a cadeia, só leem/medem.
- Decisores (auth.js): podem encerrar a requisição respondendo diretamente, sem chamar 'next()'.

## Armadilha clássica: esquecer 'next()'

Se um middleware não chama 'next()' nem responde com 'res.send/json/end', a requisição trava para sempre (o cliente fica esperando até dar timeout). É o bug mais comum ao escrever middlewares Express — toda função aqui precisa terminar de uma das duas formas, nunca as duas, nunca nenhuma.

## Por que não colocar essa lógica direto nas rotas do JSON Server?

O JSON Server não expõe rotas individuais para customizar — ele gera todo o CRUD automaticamente. Middlewares são o único ponto de extensão disponível sem reescrever o router inteiro, o que mantém o benefício de 'zero código de rota' do JSON Server intacto.
