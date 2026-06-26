Segue uma convenção parecida com BEM: a classe base '.btn' carrega o que é comum a todo botão (padding, borda, tipografia), e modificadoras como '.btn--primary' ou '.btn--ghost' alteram só cor e contraste — nunca espaçamento ou tamanho, para manter todos os botões alinhados visualmente.

```css
.btn {
    display: inline-flex; /* alinha ícone + texto na mesma linha de base */
    align-items: center;
    gap: var(--spacing-sm);
    padding: var(--spacing-sm) var(--spacing-md);
    border: 1px solid transparent; /* reserva espaço da borda mesmo sem cor,
                                       evitando salto de layout ao focar */
    border-radius: 0.375rem;
    font-size: var(--font-size-base);
    cursor: pointer;
}

.btn--primary {
    background: var(--color-accent);
    color: var(--color-bg);
}

.btn:focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: 2px;
}
```

## Por que ':focus-visible' e não ':focus'?

':focus' aplica o contorno também quando o botão é clicado com o mouse, o que muitos designers consideram visualmente poluído. ':focus-visible' mostra o contorno só quando o navegador detecta navegação por teclado (Tab) — acessível para quem navega sem mouse, sem incomodar quem usa mouse.

## Convenção BEM-like — por que modificadoras só tocam cor

Se '.btn--primary' alterasse também o padding ou o tamanho de fonte, botões de variantes diferentes ficariam desalinhados quando colocados lado a lado (ex.: 'Cancelar' e 'Confirmar' num modal). Restringir as modificadoras a cor/contraste garante uniformidade visual entre todas as variantes, deixando '.btn' como a única fonte de verdade para dimensão e espaçamento.

## Caso de uso real: variante 'ghost' para ações secundárias

```css
.btn--ghost {
    background: transparent;
    border-color: var(--color-border);
    color: var(--color-text);
}

.btn--ghost:hover {
    background: var(--color-hover);
}
```

## Border transparente como técnica de estabilidade de layout

Declarar 'border: 1px solid transparent' na classe base (mesmo quando a variante visualmente não tem borda) reserva o espaço de 1px desde o início. Sem isso, uma variante que adiciona borda no ':hover' faria o botão crescer 2px e empurrar elementos vizinhos — um problema sutil de CLS (Cumulative Layout Shift), métrica relevante de performance percebida.

## Acessibilidade adicional: contraste de cor

'--color-accent' sobre '--color-bg' precisa atingir uma razão de contraste mínima (WCAG AA recomenda 4.5:1 para texto normal) para ser legível por pessoas com baixa visão. Qualquer alteração nos tokens de cor (ver 'tokens.css') deveria ser validada contra essa razão antes de ir para produção.
