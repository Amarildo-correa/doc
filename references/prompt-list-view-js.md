Esta view amarra três camadas do projeto: 'api.js' (busca os dados), 'card.js' (renderiza cada item) e 'truncate.js' (evita títulos longos quebrando o layout). Nenhuma dessas camadas conhece as outras diretamente — a view é quem as conecta.

```js
import { getPrompts } from "../api.js";
import { createCard } from "../components/card.js";

export function PromptListView(root) {
    const grid = document.createElement("div");
    grid.className = "prompt-grid"; // estilizado em layout.css, não aqui
    root.appendChild(grid);

    getPrompts()
        .then((prompts) => {
            // spread no replaceChildren: cada prompt vira um nó de DOM
            // independente via createCard, todos inseridos numa única operação
            grid.replaceChildren(...prompts.map(createCard));
        })
        .catch((err) => {
            // Falha de rede ou de api.js (res.ok === false) cai aqui —
            // a UI degrada graciosamente em vez de travar ou ficar em branco
            grid.textContent = "Não foi possível carregar os prompts.";
            console.error(err);
        });

    return {
        destroy() {
            grid.replaceChildren(); // libera referências de DOM explicitamente
        },
    };
}
```

## Por que tratar o erro aqui e não em api.js?

'api.js' apenas lança o erro ('throw') — ele não sabe nada sobre DOM ou UI. Cabe a cada view decidir como exibir uma falha de rede, o que mantém a camada de dados reutilizável em qualquer contexto (outra view, um teste, um script de CLI).

## Caso de uso real: testando o estado de erro manualmente

Para ver esta tela de erro sem precisar derrubar o backend, basta parar temporariamente o container 'json-server' (ou usar o middleware 'request-delay.js' combinado com uma rota inválida) — o '.catch()' aqui garante que o usuário veja uma mensagem compreensível em vez de uma tela em branco ou um erro de console.

## Por que não há flag 'cancelled' nesta view (diferente de prompt-detail-view.js)

Tecnicamente, esta view tem a mesma condição de corrida potencial descrita em 'prompt-detail-view.js': se o usuário navegar para fora antes de 'getPrompts()' resolver, o '.then()' ainda escreveria no 'grid' depois do 'destroy()'. Como 'grid' é uma referência local que só pertence a esta instância da view (não compartilhada globalmente), o efeito prático é inofensivo — mas, em uma revisão de código rigorosa, vale considerar aplicar o mesmo padrão de cancelamento por consistência.

## Performance e escalabilidade

Para uma lista grande de prompts, renderizar todos os cards de uma vez ('prompts.map(createCard)') pode se tornar custoso. A evolução natural seria paginação (a API já suporta via query params do JSON Server, ex.: '?_page=1&_limit=20') ou virtualização de lista (renderizar só os itens visíveis), nenhuma delas implementada aqui propositalmente, para manter o exemplo simples.

## Acessibilidade

A mensagem de erro é definida via 'textContent', o que garante que leitores de tela a anunciem corretamente como texto simples. Uma melhoria possível seria adicionar 'role="alert"' ao 'grid' nesse cenário, para que tecnologias assistivas anunciem a mudança imediatamente, sem que o usuário precise navegar manualmente até encontrá-la.
