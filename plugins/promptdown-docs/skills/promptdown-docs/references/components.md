Construídos com a tag nativa <template>: fica inerte no DOM até ser clonado via 'document.importNode' — preferido a 'cloneNode(true)' ou innerHTML com conteúdo dinâmico, por segurança contra XSS.

```js
const template = document.querySelector("#card-template");

export function createCard(data) {
    const node = document.importNode(template.content, true);
    node.querySelector(".card__title").textContent = data.title;
    return node;
}
```

## Por que '<template>' e não construir o DOM inteiro via createElement

Escrever cada elemento de um componente complexo com 'document.createElement' linha a linha é verboso e difícil de visualizar. '<template>' permite escrever o HTML estático normalmente no próprio 'index.html' (analisado pelo navegador, porém nunca renderizado nem executado), e o JavaScript apenas clona essa estrutura pronta e injeta os dados dinâmicos via 'textContent' — o melhor dos dois mundos: HTML legível e segurança contra XSS, já que nenhum dado de usuário é interpretado como marcação.

## 'document.importNode' vs 'cloneNode(true)'

Ambos clonam profundamente um nó, mas 'template.content' pertence a um 'DocumentFragment' que tecnicamente vive fora do documento principal. 'importNode' adapta o nó clonado corretamente para o documento atual antes da inserção; 'cloneNode' direto em alguns casos de cross-document ou Shadow DOM pode se comportar de forma inesperada. O projeto padroniza em 'importNode' para evitar essa categoria de bug sutil.

## Contrato comum dos componentes desta pasta

- Recebem dados (não a store, não o router) e devolvem um nó de DOM pronto.
- Nunca leem ou escrevem na store global — permanecem reutilizáveis fora do contexto desta aplicação específica.
- Comunicam interações para fora via 'CustomEvent' com 'bubbles: true' (ver 'card.js'), nunca via callback acoplado.

## Desacoplamento e escalabilidade

Por não dependerem de 'store.js' ou 'api.js', estes componentes poderiam ser extraídos para um pacote separado e reutilizados em outro projeto sem nenhuma alteração — um teste prático de que a responsabilidade única está sendo respeitada.
