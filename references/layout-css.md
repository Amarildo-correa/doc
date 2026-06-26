Define apenas onde as coisas ficam posicionadas (cabeçalho, conteúdo, rodapé) — não cor nem tipografia de componentes específicos.

```css
.app-layout {
    display: grid;
    /* auto = altura do conteúdo; 1fr = ocupa todo o espaço restante;
       garante que o rodapé fique sempre colado ao fim da viewport
       mesmo com pouco conteúdo na página */
    grid-template-rows: auto 1fr auto;
    min-height: 100vh;
}

.prompt-grid {
    display: grid;
    /* repeat + auto-fill + minmax: quantas colunas cabem decide o
       navegador, sem precisar de media queries manuais para cada largura */
    grid-template-columns: repeat(auto-fill, minmax(16rem, 1fr));
    gap: var(--spacing-md);
}
```

## Separação entre 'onde' (layout) e 'como parece' (componente)

'.prompt-grid' aqui só decide o posicionamento (quantas colunas, espaçamento entre itens) — a aparência de cada item individual (bordas, cor de fundo, sombra) é responsabilidade de 'card.css'. Essa divisão permite reusar '.prompt-grid' para exibir qualquer tipo de card no futuro, sem acoplar a grade a um componente visual específico.

## Por que 'grid' em vez de 'flexbox' para esse caso

Flexbox é unidimensional (controla bem uma linha OU uma coluna por vez); Grid é bidimensional e nativamente entende 'quantas colunas cabem nesta largura', o que é exatamente o problema de uma grade de cards responsiva. 'repeat(auto-fill, minmax(...))' resolve isso numa única linha de CSS, sem media queries manuais para cada breakpoint.

## Performance e reflow

Mudanças de layout (adicionar/remover itens da grade) disparam reflow do navegador — inevitável em qualquer mudança estrutural de DOM. O uso de Grid aqui não piora isso comparado a Flexbox; a recomendação geral de performance é evitar mudar propriedades de layout em animações (preferir 'transform'/'opacity', que são compostas pela GPU sem reflow).
