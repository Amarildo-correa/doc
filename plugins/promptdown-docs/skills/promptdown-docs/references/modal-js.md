Uma view representa uma 'tela' completa e vive enquanto o usuário está nela; um modal é transitório — pode abrir e fechar várias vezes sem que a view por trás seja destruída. Por isso ele expõe 'open()'/'close()' em vez do contrato '{ destroy }' das views.

```js
const template = document.querySelector("#modal-template");

export function createModal({ title, content }) {
    const node = document.importNode(template.content, true);
    const overlay = node.querySelector(".modal-overlay");

    overlay.querySelector(".modal__title").textContent = title;
    // "content" é um nó de DOM já construído por quem chamou createModal —
    // o modal não sabe e não precisa saber o que está sendo exibido dentro
    overlay.querySelector(".modal__body").appendChild(content);

    function close() {
        overlay.remove(); // remove do DOM
        document.removeEventListener("keydown", onKeydown); // remove o listener global
    }

    function onKeydown(event) {
        if (event.key === "Escape") close();
    }

    overlay.querySelector(".modal__close").addEventListener("click", close);
    // Listener global em "document": necessário porque Esc deve fechar o
    // modal independente de qual elemento estiver focado no momento
    document.addEventListener("keydown", onKeydown);

    return {
        open() {
            document.body.appendChild(overlay);
        },
        close,
    };
}
```

## Vazamento de listener — o detalhe fácil de esquecer

O listener de 'keydown' é registrado em 'document', não no próprio modal. Se 'close()' não o removesse explicitamente, cada modal aberto e fechado deixaria um listener fantasma acumulando na memória — o mesmo tipo de bug que o contrato '{ destroy }' das views existe para evitar.

## Caso de uso real: confirmação de exclusão de um prompt

```js
import { createModal } from "../components/modal.js";

const content = document.createElement("p");
content.textContent = "Tem certeza que deseja excluir este prompt?";

const modal = createModal({ title: "Confirmar exclusão", content });
modal.open();
// quando o usuário confirma, o código que chamou createModal decide o
// que fazer (ex.: chamar a API de delete) e então modal.close()
```

## Por que 'open()'/'close()' em vez do contrato '{ destroy }'

Views têm exatamente um ciclo de montagem e um de desmontagem por navegação. Um modal pode abrir e fechar repetidamente dentro da MESMA view ativa (ex.: o usuário cancela e tenta excluir novamente) — usar 'destroy()' sugeriria um ciclo de vida único, o que não reflete a realidade de um componente que se repete. A API 'open/close' comunica corretamente essa natureza reentrante.

## Acessibilidade — o que falta neste exemplo

- Foco: ao abrir, o foco do teclado deveria mover-se para dentro do modal (ex.: para o botão de fechar ou primeiro elemento focável), e voltar ao elemento que abriu o modal quando ele fechar.
- Trap de foco: pressionar Tab repetidamente não deveria escapar do modal para o conteúdo por trás do overlay.
- Atributos ARIA: 'role="dialog"' e 'aria-modal="true"' no '.modal-overlay' comunicam a leitores de tela que o conteúdo por trás está temporariamente inacessível.

## Alternativa e trade-off

O elemento nativo '<dialog>' do HTML5 já oferece 'showModal()'/'close()', trap de foco e fechamento via Esc de fábrica, eliminando boa parte deste código manual — mas com suporte e estilização um pouco menos uniformes entre navegadores mais antigos, motivo provável da escolha por uma implementação própria aqui.
