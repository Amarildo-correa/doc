Numa rede local, o JSON Server responde em poucos milissegundos — tempo insuficiente para perceber visualmente um spinner ou skeleton screen. Este middleware insere um atraso proposital para tornar esses estados de loading testáveis manualmente.

```js
const DELAY_MS = Number(process.env.MOCK_DELAY_MS) || 400;

function requestDelay(req, res, next) {
    // setTimeout adia a chamada de next() — a requisição fica "pendurada"
    // no pipeline por DELAY_MS antes de seguir para o router
    setTimeout(next, DELAY_MS);
}

module.exports = requestDelay;
```

## Por que ler de uma variável de ambiente?

Hardcoded, o atraso vira um incômodo permanente durante testes automatizados (que não querem esperar). Lendo de 'process.env.MOCK_DELAY_MS', o time de QA pode ligar o atraso manualmente em uma sessão de desenvolvimento sem afetar o pipeline de CI, que simplesmente não define essa variável e cai no padrão (ou em zero).

## Caso de uso real: validando o PromptListView

Sem este middleware, seria preciso simular manualmente conexões 3G no DevTools para ver se 'prompt-list-view.js' mostra um estado de carregamento decente. Com 'MOCK_DELAY_MS=2000', qualquer desenvolvedor reproduz a sensação de uma rede lenta com um único env var, sem alterar o navegador.

## Cuidado: não confundir com Throttling de produção

Este delay é artificial e síncrono ao pipeline do servidor — ele não representa latência de rede real (round-trip, DNS, TLS). É uma ferramenta de desenvolvimento, não uma métrica de performance; não deve ser usado para tirar conclusões sobre o tempo de resposta real do backend definitivo.

## Boas práticas de configuração via env var

- Sempre prover um fallback sensato ('|| 400') para que o middleware funcione mesmo sem configuração explícita.
- Documentar a variável (README ou .env.example) para que não seja uma 'mágica' invisível ao time.
- Nunca deixar esse tipo de delay artificial ativo por padrão em pipelines de CI — testes automatizados não devem pagar esse custo de tempo sem necessidade.
