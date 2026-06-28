Componente de formulário DRY — única fonte de campos para criar e editar prompts. Recebe `initialData` para pré-popular (modo edição) ou `null` (modo criação), e comunica o submit via `CustomEvent` sem conhecer o `api.js`.

```js
const template = document.querySelector("#prompt-form-template");

// initialData: objeto com os campos do prompt (modo edição)
//              null ou undefined (modo criação — campos em branco)
export function createPromptForm(initialData = null) {
    const node = document.importNode(template.content, true);
    const form = node.querySelector(".prompt-form");

    // Pré-popula os campos se vier dados iniciais (modo edição)
    if (initialData) {
        form.querySelector("#prompt-title").value = initialData.title ?? "";
        form.querySelector("#prompt-body").value  = initialData.body  ?? "";
        form.querySelector("#prompt-tags").value  =
            (initialData.tags ?? []).join(", ");
    }

    form.addEventListener("submit", (event) => {
        event.preventDefault();

        const data = {
            title: form.querySelector("#prompt-title").value.trim(),
            body:  form.querySelector("#prompt-body").value.trim(),
            tags:  form.querySelector("#prompt-tags").value
                       .split(",")
                       .map((t) => t.trim())
                       .filter(Boolean),
        };

        // O componente não sabe se é create ou update — quem ouve o evento decide
        form.dispatchEvent(
            new CustomEvent("prompt-form:submit", {
                detail: data,
                bubbles: true, // sobe até o handler do modal ou da view
            })
        );
    });

    return form; // nó <form> pronto para appendChild em qualquer container
}
```

## Por que o componente não chama o api.js diretamente

Chamar `createPrompt()` ou `updatePrompt()` dentro do componente exigiria que ele soubesse em qual modo está — e ainda assim o caller precisaria ouvir o resultado para fechar o modal ou atualizar a lista. O `CustomEvent` inverte esse fluxo: o componente apenas anuncia "dados prontos para salvar" e qualquer ancestral no DOM decide o que fazer com eles, sem acoplamento.

## Como usar dentro de um modal (criar)

```js
import { createPromptForm } from "../components/prompt-form.js";
import { createModal }      from "../components/modal.js";
import { createPrompt }     from "../api.js";

const formNode = createPromptForm(); // modo criação

formNode.addEventListener("prompt-form:submit", async (event) => {
    await createPrompt(event.detail);
    modal.close();
    // atualizar lista localmente ou refazer GET conforme a view decidir
});

const modal = createModal({ title: "Novo prompt", content: formNode });
modal.open();
```

## Como usar dentro de um modal (editar)

```js
import { createPromptForm } from "../components/prompt-form.js";
import { createModal }      from "../components/modal.js";
import { updatePrompt }     from "../api.js";

const formNode = createPromptForm(prompt); // modo edição — campos pré-populados

formNode.addEventListener("prompt-form:submit", async (event) => {
    await updatePrompt(prompt.id, event.detail);
    modal.close();
});

const modal = createModal({ title: "Editar prompt", content: formNode });
modal.open();
```

## O que muda entre criar e editar

Nada no componente. Tudo no caller:
- Criação: `createPromptForm()` + listener chama `createPrompt()`
- Edição: `createPromptForm(prompt)` + listener chama `updatePrompt(id, data)`

Esse padrão é o DRY em ação: um único componente, dois comportamentos definidos fora dele.

## Contrato com o `<template>` no index.html

O componente depende de `#prompt-form-template` existir no `index.html` com a estrutura:

```html
<template id="prompt-form-template">
    <form class="prompt-form">
        <label for="prompt-title">Título</label>
        <input id="prompt-title" name="title" type="text" required />

        <label for="prompt-body">Conteúdo</label>
        <textarea id="prompt-body" name="body" required></textarea>

        <label for="prompt-tags">Tags (separadas por vírgula)</label>
        <input id="prompt-tags" name="tags" type="text" />

        <div class="prompt-form__actions">
            <button type="submit" class="btn btn--primary">Salvar</button>
        </div>
    </form>
</template>
```

## Estados de UI e responsabilidade

| Estado | Quem controla |
|---|---|
| Loading (botão desabilitado) | O caller desabilita o botão após ouvir o evento, antes do fetch |
| Erro de API | O caller captura o throw do api.js e exibe via `toast.js` |
| Erro de validação (campo vazio) | O `required` nativo do HTML + `input.css` (estado `:invalid`) |
| Fechar após sucesso | O caller chama `modal.close()` ou redireciona via `router.js` |

## Alternativa e trade-off

Passar `onSubmit` como callback direto (em vez de `CustomEvent`) simplificaria o caller — uma linha a menos. O trade-off é acoplar o componente a uma convenção de callback, enquanto o `CustomEvent` com `bubbles: true` deixa o formulário injetável em qualquer profundidade do DOM sem passar a função por vários níveis — mais flexível para reutilização futura.
