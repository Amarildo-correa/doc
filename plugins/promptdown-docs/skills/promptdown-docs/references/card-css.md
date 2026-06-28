O HTML do card é gerado por 'components/card.js', mas este arquivo é quem decide sua aparência: borda e espaçamento interno — a separação visual entre cards vem inteiramente do grid de bordas de 1px, sem fundo ou sombra própria. Separar estrutura (JS) de aparência (CSS) é o que permite redesenhar o card inteiro sem tocar em uma linha de JavaScript.

```css
.card {
    /* sem background-color própria — herda --color-bg do body */
    border: 1px solid var(--color-border);
    /* cantos sempre retos — nunca border-radius */
    padding: var(--space-4);
    cursor: pointer;
    /* transition só na propriedade que muda no hover: evita custo de
       recalcular outras propriedades a cada frame da animação */
    transition: border-color var(--transition-fast);
}

.card:hover {
    border-color: var(--color-accent);
}

.card__title {
    margin: 0;
    font-weight: var(--font-bold);
}
```

Note a ausência de 'width' ou 'margin' externas: quem decide o espaçamento entre cards é o container pai ('prompt-grid', definido em layout.css ou na própria view), não o card em si — outra aplicação do princípio de responsabilidade única.

## Convenção de nomenclatura: '.card' vs '.card__title'

O duplo underscore separa o bloco ('.card') de um elemento que pertence semanticamente a ele ('.card__title') — uma convenção próxima de BEM (Block-Element-Modifier). Isso evita colisão de nomes com outros componentes que também possam ter um elemento chamado 'title' (ex.: '.modal__title' em 'modal.css'), já que cada classe é prefixada pelo bloco a que pertence.

## Caso de uso real: por que a borda muda no hover, e não a sombra

Animar 'box-shadow' costuma ser mais custoso para o navegador recalcular a cada frame do que animar 'border-color' (que afeta uma área menor de repintura). Para um grid com muitos cards visíveis simultaneamente, essa escolha reduz o trabalho do navegador ao passar o mouse rapidamente por vários itens.

## Escalabilidade: variantes de card

Se o projeto precisar de um card 'destacado' (ex.: prompt em alta) no futuro, a extensão natural seria uma classe modificadora '.card--featured' que ajusta cor de borda/fundo, seguindo a mesma convenção já usada em 'button.css' com '.btn--primary' — mantendo o padrão de nomenclatura consistente entre componentes.
