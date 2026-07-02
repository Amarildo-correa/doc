Usa 'pushState' para trocar a URL sem recarregar a página e escuta 'popstate' para reagir ao botão voltar/avançar. O projeto evita deliberadamente hash routing (URLs com '#').

Na carga inicial da página, o HTML já chega pronto do Worker (SSR — ver `worker-js.md`): `#app` não está vazio, ele já contém o markup semântico da rota atual, marcado com `data-route`. Por isso o router nunca reconstrói `#app` do zero ao inicializar — ele **hidrata** o que já existe, e só passa a montar/desmontar views do zero nas navegações internas subsequentes (SPA).

```js
// Mantém referência à view atualmente montada, para poder destruí-la
// antes de montar a próxima (evita listeners/subscriptions fantasmas)
let currentView = null;

export function initRouter() {
    const root = document.getElementById("app");
    const ssrRoute = root.dataset.route; // ex.: "/prompts/:id", deixado pelo Worker

    // Hidratação: o HTML de "root" já existe (veio do SSR) — a view só
    // precisa "acoplar" listeners e estado, nunca apagar o conteúdo atual
    if (ssrRoute) {
        const match = matchRoute(location.pathname);
        currentView = match.view(root, match.params, { hydrate: true });
        return;
    }

    renderRoute(location.pathname);
}

export function navigate(path) {
    history.pushState({}, "", path); // troca a URL sem recarregar a página
    renderRoute(path);
}

function renderRoute(path) {
    const root = document.getElementById("app");

    // Lifecycle: sempre limpa a view anterior antes de montar a nova
    if (currentView?.destroy) currentView.destroy();

    const match = matchRoute(path); // decide qual view corresponde à URL
    currentView = match.view(root, match.params);
}

// "popstate" dispara quando o usuário usa os botões voltar/avançar do
// navegador — o router precisa reagir manualmente, já que não houve
// um pushState() desta vez
window.addEventListener("popstate", () => {
    renderRoute(location.pathname);
});
```

## Hidratação vs. reconstrução do zero

Só a primeira montagem (a que acontece quando `app.js` chama `initRouter()`) é uma hidratação — ela detecta `data-route` em `#app` e reaproveita o HTML já renderizado pelo Worker, evitando que humanos vejam um "flash" de tela vazia antes do JavaScript montar tudo de novo. Todas as navegações internas seguintes (clique em link, `navigate()`) continuam funcionando como SPA tradicional: `renderRoute` destrói a view anterior e monta a próxima do zero, porque não há um novo SSR para essas trocas de rota client-side.

## Por que History API e nunca hash routing

URLs com '#' (ex.: '/#/prompts/42') funcionam sem configuração de servidor, mas são tratadas pelo navegador como fragmentos da MESMA página — o que quebra convenções da web (compartilhar a URL, indexação por buscadores, analytics de página). A History API gera URLs 'reais' ('/prompts/42'), respondidas diretamente pelo `worker.js` com SSR (ver `worker-js.md`) em vez de depender de um fallback de servidor estático — em troca de uma experiência de navegação indistinguível de um site tradicional, e com o benefício extra de cada URL já chegar com HTML completo para bots e humanos.

## Caso de uso real: navegação interna vs. clique externo

```js
// Em qualquer view/componente, links internos devem chamar navigate()
// em vez de usar <a href> puro, para evitar o reload completo da página
link.addEventListener("click", (e) => {
    e.preventDefault();
    navigate(link.getAttribute("href"));
});
```

## O contrato de lifecycle das views

O router é o único consumidor direto do contrato '{ destroy }' que toda view deve implementar (ver 'views/'). Antes de montar a próxima view, ele sempre chama 'destroy()' na anterior — isso é o que impede vazamento de memória por subscriptions da store ou listeners de DOM esquecidos ao trocar de tela.

## Armadilhas comuns

- Esquecer 'e.preventDefault()' num clique de link interno causa um reload completo da página — perdendo todo o estado em memória da SPA.
- Não tratar 'popstate' faria o botão 'voltar' do navegador mudar a URL sem atualizar a tela, gerando uma divergência visível entre URL e conteúdo.
- Montar a próxima view antes de destruir a anterior pode causar dois listeners da store ativos simultaneamente, dobrando re-renderizações.

## Alternativa e trade-off

Bibliotecas como 'page.js' ou o router do React Router oferecem matching de rotas mais sofisticado (rotas aninhadas, guards, lazy loading automático). Aqui, um router caseiro mantém zero dependências externas, ao custo de o time precisar implementar manualmente qualquer funcionalidade mais avançada de roteamento.

## Fontes

- `worker-js.md`
- `index-html.md`
- `app-js.md`
