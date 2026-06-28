É a view 'raiz' à qual o router cai por padrão na rota '/'. Sua responsabilidade é orientar o usuário: mostrar um resumo do estado atual (ex.: usuário logado, prompts recentes) e oferecer entradas para as demais telas.

Como qualquer view do projeto, ela só lê o estado pela 'store' (nunca o contrário) e nunca manipula o DOM com innerHTML — usa 'textContent' para texto simples e os componentes de 'components/' para blocos de UI mais ricos.

```js
import { store, subscribe } from "../store.js";
import { getPrompts } from "../api.js";
import { createCard } from "../components/card.js";

export function HomeView(root) {
    const list = document.createElement("div");
    root.appendChild(list);

    async function render() {
        // getPrompts() vem da camada api.js — esta view não sabe (nem
        // precisa saber) que por trás existe um JSON Server num container
        const prompts = await getPrompts();

        // replaceChildren substitui todo o conteúdo de uma vez, evitando
        // duplicar cards em re-renderizações repetidas (ex.: store mudando)
        list.replaceChildren(...prompts.slice(0, 3).map(createCard));
    }

    render(); // primeira renderização ao montar a view
    const unsubscribe = subscribe(render); // re-renderiza a cada mudança de store

    return {
        destroy() {
            unsubscribe(); // contrato de lifecycle: limpa a subscription
        },
    };
}
```

## Fluxo completo de execução

-   1. Usuário acessa '/' → router.js monta 'HomeView(root)'.
-   2. 'render()' é chamado imediatamente, disparando 'getPrompts()' (assíncrono).
-   3. Enquanto a requisição está pendente, a tela permanece vazia (não há estado de loading explícito aqui — uma melhoria possível seria adicioná-lo).
-   4. Quando a Promise resolve, os 3 primeiros prompts são transformados em cards via 'createCard' e inseridos no DOM.
-   5. Se a store mudar por qualquer motivo (ex.: login), 'render()' roda de novo, buscando os dados outra vez.

## Por que 'render' é re-executado a cada mudança de store, mesmo sem depender diretamente dela

Esta view se inscreve em QUALQUER mudança de store, não só em 'store.user'. Isso é simples de implementar, mas tem um custo: toda mudança de estado, mesmo irrelevante para esta tela, dispara uma nova chamada de rede. Numa aplicação maior, a melhoria natural seria comparar se o campo relevante de fato mudou antes de re-buscar dados.

## Relação com o restante do projeto

Esta view amarra três camadas de uma só vez: 'store.js' (estado/reatividade), 'api.js' (dados) e 'components/card.js' (apresentação) — um bom exemplo de como uma view atua como 'cola', sem conter lógica de negócio profunda nela mesma.

## Armadilha não tratada aqui (ver prompt-detail-view.js)

Esta view não usa a flag 'cancelled' explicada em 'prompt-detail-view.js'. Como a navegação para fora da Home provavelmente acontece rápido o suficiente para não causar problemas práticos, o time aceitou esse risco aqui — mas é uma dívida técnica latente caso a requisição de 'getPrompts()' se torne mais lenta no futuro.
