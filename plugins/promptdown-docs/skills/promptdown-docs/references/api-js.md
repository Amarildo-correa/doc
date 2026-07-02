Centraliza todas as chamadas 'fetch' apontando para uma BASE_URL configurável. É o único ponto a mudar quando o JSON Server for substituído por um backend real.

```js
// Sem Nginx nesta arquitetura, não há mais um proxy same-origin para
// "/api" — o navegador chama a API da Vultr diretamente por uma URL
// absoluta, habilitada via CORS no server.js (ver server-js.md e api.md)
const BASE_URL = "https://api.promptdown.com";

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

export async function createPrompt(data) {
    const res = await fetch(`${BASE_URL}/prompts`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data),
    });
    if (!res.ok) throw new Error("Falha ao criar prompt");
    return res.json(); // JSON Server devolve o objeto criado com o id gerado
}

export async function updatePrompt(id, data) {
    // PATCH atualiza só os campos enviados; PUT substituiria o objeto inteiro
    const res = await fetch(`${BASE_URL}/prompts/${id}`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(data),
    });
    if (!res.ok) throw new Error(`Falha ao atualizar prompt ${id}`);
    return res.json();
}

export async function deletePrompt(id) {
    const res = await fetch(`${BASE_URL}/prompts/${id}`, {
        method: "DELETE",
    });
    if (!res.ok) throw new Error(`Falha ao excluir prompt ${id}`);
    // DELETE bem-sucedido retorna 200 com {} — não há dado útil para devolver
}
```

## Por que centralizar fetch em um único módulo

Se cada view chamasse 'fetch' diretamente, a URL base, o tratamento de erro e qualquer header comum (ex.: 'Authorization') estariam duplicados em N lugares. Centralizando em 'api.js', trocar o backend real significa editar este único arquivo — nenhuma view precisa saber que a URL mudou, por exemplo, de 'https://api.promptdown.com' para 'https://api.promptdown.com/v2'.

## Caso de uso real: troca de backend sem tocar nas views

Quando o backend real (não-mock) for implementado, é bastante provável que a forma de autenticação mude (de header fixo para token dinâmico, por exemplo). Esse ajuste acontece inteiramente dentro de 'api.js' — 'prompt-list-view.js' e 'home-view.js' continuam chamando 'getPrompts()' exatamente como antes, sem saber que algo mudou por debaixo.

## Por que o erro é lançado aqui e tratado na view

'api.js' lança ('throw') o erro mas não decide como exibi-lo — isso é responsabilidade de quem chama (ver 'prompt-list-view.js' tratando com '.catch()'). Essa separação mantém 'api.js' livre de qualquer dependência de DOM, o que o torna testável isoladamente (mockando 'fetch') e reutilizável em qualquer contexto, até num script Node sem navegador.

## Performance: por que não cachear aqui

Decidir cache de requisições dentro de 'api.js' acoplaria essa camada a decisões de UX (quando invalidar, por quanto tempo). O projeto prefere deixar cada view decidir se precisa cachear, mantendo 'api.js' como uma camada fina e previsível — uma chamada, uma requisição, sempre.

## Segurança

Como o frontend (Cloudflare) e a API (Vultr) vivem em domínios diferentes, toda chamada é cross-origin — o `server.js` habilita CORS via `jsonServer.defaults()` (ver `server-js.md`) para permitir isso. Diferente do proxy same-origin que o Nginx fazia antes, `BASE_URL` precisa ser explicitamente diferente por ambiente (dev vs. produção), então evitar hardcode aqui é ainda mais importante — o valor deve vir de uma variável de build/ambiente, nunca de uma string fixa espalhada pelo código.

## CRUD completo — decisões de design

### Por que PATCH e não PUT em updatePrompt

PUT substitui o objeto inteiro — se o formulário de edição não enviar todos os campos (ex.: omitir 'createdAt'), o JSON Server apagaria esses campos do registro. PATCH faz merge: só os campos presentes no body são tocados, os demais permanecem intactos. Para formulários que editam campos isolados isso é mais seguro.

### Por que createPrompt devolve o objeto criado

O JSON Server atribui o 'id' automaticamente e devolve o registro completo no corpo da resposta do POST. A view que chama 'createPrompt()' pode usar esse retorno para, por exemplo, redirecionar imediatamente ao detalhe do novo prompt sem precisar fazer um segundo GET.

### Por que deletePrompt não tem return

Um DELETE bem-sucedido retorna HTTP 200 com body '{}' — não há dado útil. A view confirma o sucesso pelo fato de a Promise ter resolvido sem lançar erro, e pode então remover o item da lista localmente sem refazer o GET completo.

### Padrão comum às quatro operações

Todas seguem o mesmo contrato: (1) faz o fetch com o método correto, (2) verifica 'res.ok' e lança se falhou, (3) devolve o JSON ou nada. Esse padrão uniforme facilita escrever um wrapper genérico de tratamento de erro na view.

## Alternativa e trade-off

Bibliotecas como 'axios' adicionam interceptors, cancelamento de requisição e retries automáticos prontos. Aqui, 'fetch' nativo evita uma dependência, ao custo de o time precisar implementar manualmente qualquer uma dessas funcionalidades caso se tornem necessárias.
