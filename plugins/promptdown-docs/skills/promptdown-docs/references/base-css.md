Normaliza diferenças entre navegadores antes de qualquer estilo de componente ser aplicado — sempre o primeiro CSS 'de verdade' carregado, depois dos tokens.

```css
* {
    box-sizing: border-box; /* padding/border somam DENTRO da largura
                               declarada, evitando surpresas de layout */
}

body {
    margin: 0; /* navegadores aplicam margem default diferente entre si */
    font-family: system-ui, sans-serif;
    color: var(--color-text); /* já consumindo um token, não um valor fixo */
}
```

## Por que um reset é necessário

Cada navegador vem com uma folha de estilo padrão (user agent stylesheet) ligeiramente diferente — margens de heading, espaçamento de lista, tamanho de fonte de botão variam entre Chrome, Firefox e Safari. Sem um reset, o mesmo HTML pode parecer visualmente distinto em navegadores diferentes; 'base.css' nivela esse ponto de partida antes de qualquer estilo intencional do projeto ser aplicado.

## 'box-sizing: border-box' — o detalhe que evita um bug clássico

Sem essa regra, definir 'width: 100%' num elemento com 'padding' faz o elemento ficar maior que o container pai (padding soma POR FORA da largura). Com 'border-box', padding e borda são contados DENTRO da largura declarada — o comportamento intuitivo que a maioria dos desenvolvedores espera ao definir dimensões.

## Por que isto vem antes de layout.css e dos componentes

Se 'base.css' fosse carregado depois, ele poderia sobrescrever acidentalmente ajustes finos feitos em componentes específicos (por ter regras mais genéricas, mas declaradas depois — lembrando que CSS resolve empates de especificidade pela ordem). Carregá-lo primeiro garante que ele é sempre a 'base' a ser refinada pelas camadas seguintes, nunca o contrário.

## Alternativa e trade-off

Resets prontos como 'normalize.css' ou 'modern-css-reset' cobrem mais casos de borda entre navegadores antigos. Um reset caseiro minimalista, como aqui, é suficiente quando o público-alvo usa navegadores modernos, evitando peso e regras desnecessárias para casos que o projeto não precisa cobrir.
