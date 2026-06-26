Toda view segue um contrato de lifecycle: retorna '{ destroy }' ou null. Quando o router troca de view, chama 'destroy()' na anterior, que cancela suas subscriptions na store.

```js
export function HomeView(root) {
    // Array de "funções de limpeza" — cada subscribe() devolve sua
    // própria unsubscribe; guardamos todas para chamar no destroy()
    const unsubs = [subscribe(render)];
    render(); // primeira renderização, sem esperar uma mudança de estado

    function render() {
        root.textContent = store.user?.name ?? "Visitante";
    }

    return {
        destroy() {
            unsubs.forEach((fn) => fn()); // desinscreve de TODAS as subscriptions
        },
    };
}
```

## Por que esse contrato '{ destroy }' é o coração da arquitetura

Sem framework, não existe nada nativo no navegador que destrua automaticamente listeners e subscriptions quando um elemento é removido do DOM. O contrato '{ destroy }' é a solução manual do projeto para esse problema: toda vez que o router troca de tela (ver 'router.js'), ele chama 'destroy()' explicitamente antes de montar a próxima view, evitando o vazamento de memória que aconteceria se subscriptions antigas continuassem ativas.

## Por que retornar 'null' é uma opção válida

Uma view que não se inscreve em nada (não chama 'subscribe()', não registra listeners de DOM fora do próprio 'root') não tem nada para limpar — pode legitimamente devolver 'null' em vez de um objeto vazio '{ destroy() {} }'. O router trata os dois casos da mesma forma ('currentView?.destroy?.()'), então 'null' é tão correto quanto um destroy vazio, só mais direto.

## Anatomia padrão de uma view

- Recebe 'root' (o elemento '#app') e, opcionalmente, parâmetros de rota extraídos da URL.
- Cria seus próprios elementos DOM e os anexa a 'root' — nunca manipula elementos fora do que recebeu.
- Se lê da store, sempre via 'subscribe()' — nunca lê 'store.X' uma única vez esperando que isso baste para sempre.
- Devolve '{ destroy }' (ou null) para encerrar de forma limpa qualquer recurso que tenha aberto.

## Armadilha clássica: condição de corrida em requisições assíncronas

Se o usuário navega para fora de uma view antes de uma 'fetch' pendente terminar, a Promise ainda resolve depois do 'destroy()' já ter rodado — escrever no 'root' nesse momento tentaria atualizar um DOM que já não está mais na tela. 'prompt-detail-view.js' resolve isso com uma flag 'cancelled', um padrão que vale repetir em qualquer view com requisição assíncrona.

## Alternativa e trade-off

Frameworks como Vue/React/Svelte automatizam esse lifecycle via 'onUnmounted'/'useEffect cleanup'/'onDestroy' — o desenvolvedor escreve menos código de limpeza manual, mas perde visibilidade explícita sobre quando e por que algo é destruído. Aqui, o contrato manual é mais verboso, porém deixa o ciclo de vida totalmente transparente e sem 'mágica' de framework.
