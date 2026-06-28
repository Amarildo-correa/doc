Dado um texto e um limite de caracteres, sempre retorna o mesmo resultado — sem depender de DOM, rede ou estado, o que a torna trivial de testar isoladamente.

```js
export function truncate(text, maxLength) {
    // Guarda contra entrada não-string ou já dentro do limite: devolve
    // o valor original sem modificação (não lança erro, não trava a UI)
    if (typeof text !== "string" || text.length <= maxLength) return text;

    // slice() nunca muta a string original (strings são imutáveis em JS);
    // o "…" sinaliza visualmente que o texto foi cortado
    return `${text.slice(0, maxLength)}…`;
}
```

```js
describe("truncate", () => {
    it("corta texto maior que o limite", () => {
        expect(truncate("abcdefgh", 4)).toBe("abcd…");
    });

    it("não altera texto dentro do limite", () => {
        expect(truncate("abc", 4)).toBe("abc");
    });

    it("lida com entrada não-string sem lançar erro", () => {
        expect(truncate(null, 4)).toBe(null);
    });
});
```

## Caso de uso real: títulos de prompt no card

'card.js' usa esta função para evitar que um título de prompt muito longo quebre o layout do grid (ver 'card.css', que define largura fixa para o card). Sem truncamento, um título de 200 caracteres estouraria a altura do card e desalinharia visualmente toda a grade.

## Por que a guarda de tipo (typeof text !== 'string') importa

Dados vindos de uma API mock (JSON Server) podem eventualmente conter um campo nulo ou de tipo inesperado, caso o arquivo 'database.json' seja editado manualmente de forma inconsistente. Sem essa guarda, '.slice()' lançaria 'TypeError: Cannot read properties of null', quebrando a renderização do card inteiro em vez de simplesmente exibir o valor como veio.

## Acessibilidade

Truncar visualmente o texto não deve impedir o acesso ao conteúdo completo — uma boa prática complementar (não implementada aqui, mas recomendada) é manter o atributo 'title' do elemento com o texto integral, para que o usuário possa ver o título completo ao passar o mouse, sem prejudicar quem usa leitor de tela.

## Alternativa e trade-off

CSS puro ('text-overflow: ellipsis' com 'overflow: hidden' e 'white-space: nowrap') resolveria o truncamento visualmente sem JavaScript, mas exigiria que o elemento tenha largura fixa conhecida — truncar no JS dá controle sobre exatamente quantos caracteres aparecem, independente de CSS, e funciona bem quando o limite é definido por regra de negócio (ex.: 'máximo 40 caracteres') e não por espaço disponível na tela.
