Centraliza todas as chamadas 'fetch' apontando para uma BASE_URL configurável. É o único ponto a mudar quando o JSON Server for substituído por um backend real.

```js
// "/api" é resolvido pelo proxy do Nginx (ver public.lesson) — o
// navegador nunca precisa saber o hostname real do container json-server
const BASE_URL = "/api";

export async function getPrompts() {
    const res = await fetch(`${BASE_URL}/prompts`);

    // fetch() só rejeita a Promise em falha de REDE — um 404/500 ainda
    // chega aqui como res.ok === false, então a verificação é manual
    if (!res.ok) throw new Error("Falha ao buscar prompts");

    return res.json();
}

export async function getPromptById(id) {
    const res = await fetch(`${BASE_URL}/prompts/${id}`);
    if (!res.ok) throw new Error(`Prompt ${id} não encontrado`);
    return res.json();
}
```

## Por que centralizar fetch em um único módulo

Se cada view chamasse 'fetch' diretamente, a URL base, o tratamento de erro e qualquer header comum (ex.: 'Authorization') estariam duplicados em N lugares. Centralizando em 'api.js', trocar o backend real significa editar este único arquivo — nenhuma view precisa saber que a URL mudou de '/api' para, digamos, 'https://api.promptdown.com/v2'.

## Caso de uso real: troca de backend sem tocar nas views

Quando o backend real (não-mock) for implementado, é bastante provável que a forma de autenticação mude (de header fixo para token dinâmico, por exemplo). Esse ajuste acontece inteiramente dentro de 'api.js' — 'prompt-list-view.js' e 'home-view.js' continuam chamando 'getPrompts()' exatamente como antes, sem saber que algo mudou por debaixo.

## Por que o erro é lançado aqui e tratado na view

'api.js' lança ('throw') o erro mas não decide como exibi-lo — isso é responsabilidade de quem chama (ver 'prompt-list-view.js' tratando com '.catch()'). Essa separação mantém 'api.js' livre de qualquer dependência de DOM, o que o torna testável isoladamente (mockando 'fetch') e reutilizável em qualquer contexto, até num script Node sem navegador.

## Performance: por que não cachear aqui

Decidir cache de requisições dentro de 'api.js' acoplaria essa camada a decisões de UX (quando invalidar, por quanto tempo). O projeto prefere deixar cada view decidir se precisa cachear, mantendo 'api.js' como uma camada fina e previsível — uma chamada, uma requisição, sempre.

## Segurança

Por usar caminho relativo ('/api', não uma URL absoluta com domínio fixo), este código nunca expõe acidentalmente um endpoint de outro ambiente (ex.: produção sendo chamada a partir de localhost) — o navegador resolve '/api' sempre contra o domínio atual.

## Alternativa e trade-off

Bibliotecas como 'axios' adicionam interceptors, cancelamento de requisição e retries automáticos prontos. Aqui, 'fetch' nativo evita uma dependência, ao custo de o time precisar implementar manualmente qualquer uma dessas funcionalidades caso se tornem necessárias.
