Recebe um objeto de dados e devolve um nó de DOM pronto para ser inserido — nunca toca em 'document.body' diretamente nem sabe em qual view está sendo usado. Essa independência é o que o torna reutilizável em 'home-view.js' e em 'prompt-list-view.js' ao mesmo tempo.

```js
import { truncate } from "../lib/truncate.js";

const template = document.querySelector("#card-template");

export function createCard(prompt) {
    const node = document.importNode(template.content, true);
    const root = node.querySelector(".card");

    // truncate() evita que um título longo quebre o layout do grid
    root.querySelector(".card__title").textContent = truncate(prompt.title, 40);

    // dataset.id facilita inspeção/debug no DevTools e dá um hook
    // estável para testes (querySelector(`[data-id="${id}"]`))
    root.dataset.id = prompt.id;

    root.addEventListener("click", () => {
        // bubbles: true → o evento sobe pela árvore do DOM até qualquer
        // ancestral que queira escutar, sem o card conhecer quem é esse ancestral
        root.dispatchEvent(new CustomEvent("card:select", { bubbles: true, detail: prompt.id }));
    });

    return node;
}
```

## Por que disparar um CustomEvent em vez de receber um callback?

Passar uma função 'onSelect' como parâmetro funcionaria, mas acoplaria o componente a quem o chama. Com 'CustomEvent' + 'bubbles: true', qualquer ancestral (a view, ou até um wrapper de testes) pode ouvir 'card:select' sem que 'card.js' precise saber quem está escutando.

## Caso de uso real: a view escutando o evento

```js
// Em prompt-list-view.js, por exemplo:
grid.addEventListener("card:select", (event) => {
    navigate(`/prompts/${event.detail}`); // detail carrega o id do prompt
});
```

## Por que 'truncate' é importado de 'lib/' e não reimplementado aqui

Reescrever a lógica de corte de texto dentro de 'card.js' duplicaria comportamento que outras partes da UI também podem precisar (ex.: um futuro componente de breadcrumb). Importar de 'lib/' garante que a regra de truncamento ('40 caracteres + …') seja consistente em toda a aplicação e testável uma única vez.

## Segurança: por que 'textContent' e nunca 'innerHTML' aqui

'prompt.title' vem de uma fonte de dados externa (a API mock, eventualmente um backend real com conteúdo gerado por usuários). Se fosse inserido via 'innerHTML', um título malicioso como '<img src=x onerror=alert(1)>' executaria como HTML/JS no navegador de quem visualiza o card — um clássico ataque de XSS armazenado. 'textContent' trata qualquer valor estritamente como texto, neutralizando esse vetor por completo.

## Acessibilidade

O card reage a clique do mouse, mas o trecho mostrado não adiciona 'tabindex' nem um handler de teclado ('Enter'/'Space') — para ser totalmente acessível via teclado, o elemento '.card' precisaria de 'tabindex="0"' e um listener de 'keydown' equivalente ao de clique, ou ser semanticamente um '<button>'.
