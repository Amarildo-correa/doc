Função pura que recebe uma data (string ISO ou objeto `Date`) e devolve uma string formatada para exibição na UI — sem depender de DOM, rede ou fuso horário do servidor, o que a torna trivial de testar isoladamente.

```js
export function formatDate(value) {
    const date = value instanceof Date ? value : new Date(value);

    // Guarda contra entrada inválida: devolve string vazia em vez de
    // lançar erro ou exibir "Invalid Date" cru na tela
    if (Number.isNaN(date.getTime())) return "";

    return date.toLocaleDateString("pt-BR", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
    });
}
```

```js
describe("formatDate", () => {
    it("formata uma data ISO válida", () => {
        expect(formatDate("2026-06-25")).toBe("25/06/2026");
    });

    it("formata um objeto Date válido", () => {
        expect(formatDate(new Date(2026, 5, 25))).toBe("25/06/2026");
    });

    it("retorna string vazia para entrada inválida", () => {
        expect(formatDate("não é uma data")).toBe("");
    });

    it("retorna string vazia para entrada nula", () => {
        expect(formatDate(null)).toBe("");
    });
});
```

## Caso de uso real: data de criação no card e no detalhe

'card.js' e 'prompt-detail-view.js' usam esta função para exibir a data de criação de um prompt (campo vindo de 'database.json') em um formato legível para usuários brasileiros, em vez do formato ISO bruto retornado pela API mock.

## Por que a guarda contra `Invalid Date` importa

Como o valor recebido pode vir de uma API mock cujo `database.json` foi editado manualmente, um campo de data malformado não deve quebrar a renderização do card inteiro — a função degrada de forma segura, devolvendo string vazia em vez de propagar `"Invalid Date"` para a tela ou lançar uma exceção.

## Alternativa e trade-off

Uma biblioteca como `date-fns` ou `Intl.DateTimeFormat` diretamente daria mais controle sobre formatos e locales, mas para este projeto — que evita dependências externas sempre que possível — `toLocaleDateString` nativo já cobre a necessidade sem aumentar o bundle.
