Usar 'var(--color-bg)' em vez de valores fixos significa que trocar o tema é uma mudança em um único arquivo, não uma busca-e-substitui em dezenas de arquivos.

```css
:root {
    /* Cores: nomeadas pela função (bg, accent) e não pelo valor visual
       (azul, cinza) — assim o nome continua fazendo sentido mesmo que
       a cor exata mude num futuro re-design */
    --color-bg: #1a1b26;
    --color-accent: #7aa2f7;

    /* Espaçamento numa escala consistente, nunca valores soltos como
       13px ou 22px espalhados pelo CSS */
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;

    --font-size-base: 1rem;
}
```

## Design tokens — o conceito por trás do nome do arquivo

'Design token' é o termo usado por sistemas de design (Material, Carbon, etc.) para um valor de estilo nomeado e centralizado, em vez de um valor 'mágico' espalhado pelo código. A vantagem prática: mudar '--color-accent' uma vez aqui propaga instantaneamente para botões, links e bordas em destaque por toda a aplicação, sem tocar em nenhum outro arquivo CSS.

## Por que 'rem' e não 'px' (convenção do projeto)

'rem' é relativo ao tamanho de fonte raiz do documento ('html { font-size }'), não a um valor absoluto de pixels. Se um usuário aumenta o tamanho de fonte padrão do navegador (uma necessidade comum de acessibilidade visual), elementos dimensionados em 'rem' escalam proporcionalmente; em 'px', eles permaneceriam do mesmo tamanho fixo, ignorando essa preferência de acessibilidade do usuário.

## Caso de uso real: dark mode/light mode (extensão futura)

```css
/* Um segundo tema poderia sobrescrever os mesmos nomes de variável,
   sem que button.css/card.css/modal.css precisem saber que o tema mudou */
[data-theme="light"] {
    --color-bg: #ffffff;
    --color-accent: #2f5fd6;
}
```

## Armadilha comum

Definir uma variável aqui e, ainda assim, hardcodar o valor equivalente em outro arquivo CSS 'só para ajustar um detalhe' quebra a centralização — qualquer ajuste futuro do token não vai refletir nesse lugar esquecido, criando inconsistência visual silenciosa.
