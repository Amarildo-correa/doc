Uma função pura sempre retorna o mesmo resultado para a mesma entrada e não modifica nada fora de si mesma — fácil de testar e de reutilizar em qualquer lugar.

```js
// pura: mesma entrada, mesma saída, sem efeitos colaterais
function sum(a, b) {
    return a + b;
}
```

## Por que isolar funções puras numa pasta própria

'lib/' é a base da pirâmide de dependências do projeto (ver a lesson de 'js/'): nada aqui importa de 'store.js', 'api.js' ou do DOM. Essa restrição deliberada é o que garante que qualquer função desta pasta possa ser testada com Vitest sem precisar simular navegador, rede ou estado global — apenas chamar a função e comparar o retorno.

```js
import { describe, it, expect } from "vitest";
import { sum } from "./math.js";

// Testar função pura não exige mocks: só entrada e saída
describe("sum", () => {
    it("soma dois números positivos", () => {
        expect(sum(2, 3)).toBe(5);
    });
});
```

## Impuro vs. puro — o contraste que justifica a regra

- Impuro: lê 'Date.now()', 'Math.random()', o DOM, ou modifica um parâmetro recebido — o resultado varia entre chamadas idênticas.
- Puro: depende só dos argumentos recebidos e devolve um novo valor, nunca modificando o que recebeu (ver 'truncate.js' devolvendo uma nova string em vez de truncar a original).

## Armadilha comum: 'function impura disfarçada de pura'

Um erro sutil é uma função que parece pura mas modifica (muta) um array ou objeto recebido por referência via '.push()' ou atribuição direta de propriedade. Isso quebra a previsibilidade que justifica a existência desta pasta — toda função aqui deve devolver dados novos, nunca alterar os que recebeu.

## Escalabilidade

Conforme o projeto cresce, esta pasta tende a acumular funções de formatação, validação e transformação de dados compartilhadas entre múltiplas views e componentes — centralizá-las aqui evita duplicação de lógica que, sem essa disciplina, tenderia a ser reescrita ligeiramente diferente em cada arquivo que precisasse dela.
